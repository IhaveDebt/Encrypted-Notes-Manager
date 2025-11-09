using System;
using System.IO;
using System.Security.Cryptography;
using System.Text;

namespace SecureNotes
{
    class Program
    {
        private static readonly string key = "StrongSecretKey123!";

        static void Main()
        {
            Console.Write("Enter note: ");
            string note = Console.ReadLine();

            string encrypted = Encrypt(note);
            File.WriteAllText("note.enc", encrypted);
            Console.WriteLine("Note encrypted and saved to 'note.enc'.");

            Console.Write("Decrypt note? (y/n): ");
            if (Console.ReadLine().ToLower() == "y")
            {
                string content = File.ReadAllText("note.enc");
                Console.WriteLine("Decrypted Note: " + Decrypt(content));
            }
        }

        static string Encrypt(string plain)
        {
            using (Aes aes = Aes.Create())
            {
                byte[] keyBytes = Encoding.UTF8.GetBytes(key.PadRight(32));
                aes.Key = keyBytes;
                aes.IV = new byte[16];
                var encryptor = aes.CreateEncryptor();

                byte[] plainBytes = Encoding.UTF8.GetBytes(plain);
                byte[] cipherBytes = encryptor.TransformFinalBlock(plainBytes, 0, plainBytes.Length);

                return Convert.ToBase64String(cipherBytes);
            }
        }

        static string Decrypt(string cipher)
        {
            using (Aes aes = Aes.Create())
            {
                byte[] keyBytes = Encoding.UTF8.GetBytes(key.PadRight(32));
                aes.Key = keyBytes;
                aes.IV = new byte[16];
                var decryptor = aes.CreateDecryptor();

                byte[] cipherBytes = Convert.FromBase64String(cipher);
                byte[] plainBytes = decryptor.TransformFinalBlock(cipherBytes, 0, cipherBytes.Length);

                return Encoding.UTF8.GetString(plainBytes);
            }
        }
    }
}

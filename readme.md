Username

Password

using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

class MainClass {
    public static void Main(string[] args) {
        int targetLength = 6;
        char[] charset = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789".ToCharArray();
        string username = "targetusername";

        Tuple<string, string> passwordAndUsername = CrackPasswordAndUsername(targetLength, charset, username);

        if (string.IsNullOrEmpty(passwordAndUsername.Item1) && string.IsNullOrEmpty(passwordAndUsername.Item2)) {
            Console.WriteLine("A senha e o usuário não pôdem ser desbloqueados.");
        } else {
            if (!string.IsNullOrEmpty(passwordAndUsername.Item1)) {
                Console.WriteLine("Senha desbloqueada: " + passwordAndUsername.Item1);
            }

            if (!string.IsNullOrEmpty(passwordAndUsername.Item2)) {
                Console.WriteLine("Usuário desbloqueado: " + passwordAndUsername.Item2);
            }
        }
    }

    public static Tuple<string, string> CrackPasswordAndUsername(int targetLength, char[] charset, string username) {
        string password = null;
        string user = null;
        int length = charset.Length;
        int iterations = (int)Math.Pow(length, targetLength);

        Parallel.For(0, iterations, i => {
            string candidate = GenerateCandidate(targetLength, charset, i);

            if (candidate == "targetpassword") {
                password = candidate;
            }

            if (username == candidate) {
                user = candidate;
            }

            if (!string.IsNullOrEmpty(password) && !string.IsNullOrEmpty(user)) {
                throw new OperationCanceledException();
            }
        });

        return new Tuple<string, string>(password, user);
    }

    public static string GenerateCandidate(int targetLength, char[] charset, int i) {
        char[] candidate = new char[targetLength];
        int charCount = charset.Length;

        for (int j = targetLength - 1; j >= 0; j--) {
            candidate[j] = charset[i % charCount];
            i /= charCount;
        }

        return new string(candidate);
    }
}

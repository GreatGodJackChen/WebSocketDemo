using System;
using System.Net.WebSockets;
using System.Text;
using System.Threading;

namespace ConsoleApp1
{
    class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine("Hello World!");
            var client = new ClientWebSocket();
            client.ConnectAsync(new Uri("wss://acs.m.taobao.com/accs/auth?token=AAAHXZipLerDsoyT8AUThtfqjbHYbN4RpJW9G9DWNCTOs2AhknFJIQ1TVJT+vgAOo5dkOD05uBKjUi9OYdFJnxknwYbQ/wk="), CancellationToken.None).Wait();
            //client.ConnectAsync(new Uri("wss://echo.websocket.org"), CancellationToken.None).Wait();
            StartReceiving(client);

            string line;
            while ((line = Console.ReadLine()) != "exit")
            {
                var array = new ArraySegment<byte>(Encoding.UTF8.GetBytes(line));
                client.SendAsync(array, WebSocketMessageType.Text, true, CancellationToken.None);
            }

        }

        static async void StartReceiving(ClientWebSocket client)
        {
            try
            {
                while (true)
                {
                    var tt = client.State;
                    var array = new byte[14096];
                    var result = await client.ReceiveAsync(new ArraySegment<byte>(array), CancellationToken.None);
                    if (result.MessageType == WebSocketMessageType.Text)
                    {
                        string msg = Encoding.UTF8.GetString(array, 0, result.Count);
                        Console.ForegroundColor = ConsoleColor.DarkBlue;
                        Console.WriteLine("--> {0}", msg);
                        Console.ForegroundColor = ConsoleColor.DarkGray;
                    }
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine("错误：" + ex.Message);
            }
        }
    }
}

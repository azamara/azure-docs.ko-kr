---
title: Azure Cognitive Services용 C# 빠른 시작, Microsoft Translator Speech API | Microsoft Docs
description: Azure의 Microsoft Cognitive Services에서 Microsoft Translator Speech API를 사용하여 빠르게 시작할 수 있도록 정보 및 코드 샘플을 가져옵니다.
services: cognitive-services
documentationcenter: ''
author: v-jaswel
ms.service: cognitive-services
ms.component: translator-speech
ms.topic: article
ms.date: 3/5/2018
ms.author: v-jaswel
ms.openlocfilehash: 4f12d74aedbcadc311cd9c5ccd12dc1ad3501dbf
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2018
ms.locfileid: "39205105"
---
# <a name="quickstart-for-microsoft-translator-speech-api-with-c"></a>C#을 사용한 Microsoft Translator Speech API 빠른 시작 
<a name="HOLTop"></a>

이 문서에서는 Microsoft Translator Speech API를 사용하여 .wav 파일에서 발화된 단어를 변환하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

Windows에서 이 코드를 실행하려면 [Visual Studio 2017](https://www.visualstudio.com/downloads/)이 필요합니다. (Community Edition 평가판이 작동합니다.)

아래 코드에서 컴파일하는 실행 파일과 동일한 폴더에 "speak.wav"라는 .wav 파일이 필요합니다. 이 .wav 파일은 표준 PCM, 16비트, 16kHz, 모노 형식이어야 합니다. 이러한 .wav 파일은 [Text to Speech API](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/rest-apis#text-to-speech)에서 가져올 수 있습니다.

**Microsoft Translator Speech API**와 함께 [Cognitive Services API 계정](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)이 있어야 합니다. [Azure 대시보드](https://portal.azure.com/#create/Microsoft.CognitiveServices)에서 유료 구독 키가 필요합니다.

## <a name="translate-speech"></a>음성 변환

다음 코드에서는 음성을 한 언어에서 다른 언어로 변환합니다.

1. 즐겨찾는 IDE에서 새 C# 프로젝트를 만듭니다.
2. 아래 제공된 코드를 추가합니다.
3. `key` 값을 구독에 유효한 액세스 키로 바꿉니다.
4. 프로그램을 실행합니다.

```csharp
using System;
using System.IO;
using System.Net.WebSockets;
using System.Text;
using System.Threading;
using System.Threading.Tasks;

namespace TranslateSpeechQuickStart
{
    class Program
    {
        static string host = "wss://dev.microsofttranslator.com";
        static string path = "/speech/translate";

        // NOTE: Replace this example key with a valid subscription key.
        static string key = "ENTER KEY HERE";

        async static Task Send (ClientWebSocket client, string input_path)
        {
            var audio = File.ReadAllBytes(input_path);
            var audio_out_buffer = new ArraySegment<byte>(audio);
            Console.WriteLine("Sending audio.");
            await client.SendAsync(audio_out_buffer, WebSocketMessageType.Binary, true, CancellationToken.None);

            /* Make sure the audio file is followed by silence.
             * This lets the service know that the audio input is finished. */
            var silence = new byte[3200000];
            var silence_buffer = new ArraySegment<byte>(silence);
            await client.SendAsync(silence_buffer, WebSocketMessageType.Binary, true, CancellationToken.None);

            Console.WriteLine("Done sending.");
            await client.CloseAsync(WebSocketCloseStatus.NormalClosure, "", CancellationToken.None);
        }

        async static Task Receive(ClientWebSocket client, string output_path)
        {
            var inbuf = new byte[102400];
            var segment = new ArraySegment<byte>(inbuf);
            var stream = new FileStream(output_path, FileMode.Create);

            Console.WriteLine("Awaiting response.");
            while (client.State == WebSocketState.Open)
            {
                var result = await client.ReceiveAsync(segment, CancellationToken.None);
                switch (result.MessageType)
                {
                    case WebSocketMessageType.Close:
                        Console.WriteLine("Received close message. Status: " + result.CloseStatus + ". Description: " + result.CloseStatusDescription);
                        await client.CloseAsync(WebSocketCloseStatus.NormalClosure, string.Empty, CancellationToken.None);
                        break;
                    case WebSocketMessageType.Text:
                        Console.WriteLine("Received text.");
                        Console.WriteLine(Encoding.UTF8.GetString(inbuf).TrimEnd('\0'));
                        break;
                    case WebSocketMessageType.Binary:
                        Console.WriteLine("Received binary data: " + result.Count + " bytes.");
                        stream.Write(inbuf, 0, result.Count);
                        break;
                }
            }

            stream.Close();
            stream.Dispose();
        }

        async static void TranslateSpeech()
        {
            var client = new ClientWebSocket();
            client.Options.SetRequestHeader ("Ocp-Apim-Subscription-Key", key);

            string from = "en-US";
            string to = "it-IT";
            string features = "texttospeech";
            string voice = "it-IT-Elsa";
            string api = "1.0";

            string input_path = "speak.wav";
            string output_path = "speak2.wav";

            string uri = host + path +
                "?from=" + from +
                "&to=" + to +
                "&api-version=" + api +
                "&features=" + features +
                "&voice=" + voice;

            Console.WriteLine("uri: " + uri);
            Console.WriteLine("Opening connection.");
            await client.ConnectAsync(new Uri(uri), CancellationToken.None);
            Console.WriteLine("Connection open.");
            Task.WhenAll(Send(client, input_path), Receive(client, output_path)).Wait();
        }

        static void Main()
        {
            TranslateSpeech();
            Console.ReadLine();
        }

    }
}
```

**음성 응답 변환**

성공적인 결과는 "speak2.wav"라는 파일을 만드는 것입니다. 이 파일에는 "speak.wav"에서 발화된 단어에 대한 변환이 포함됩니다.

[맨 위로 이동](#HOLTop)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Translator Speech 자습서](../tutorial-translator-speech-csharp.md)

## <a name="see-also"></a>참고 항목 

[Translator Speech 개요](../overview.md)
[API 참조](http://docs.microsofttranslator.com/speech-translate.html)

---
title: Odesílání událostí do služby Azure Event Hubs pomocí jazyka C | Dokumentace Microsoftu
description: Odesílání událostí do služby Azure Event Hubs pomocí jazyka C
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: c
ms.devlang: csharp
ms.topic: article
ms.date: 12/4/2017
ms.author: shvija
ms.openlocfilehash: 25da8255af6a23a4f01db5a1ec4f1ddcd2eeb1bb
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2018
ms.locfileid: "40002453"
---
# <a name="send-events-to-azure-event-hubs-using-c"></a>Odesílání událostí do služby Azure Event Hubs pomocí jazyka C

## <a name="introduction"></a>Úvod
Event Hubs je vysoce škálovatelná služba systému, která dokáže ingestovat miliony událostí za sekundu a umožňuje aplikaci zpracovávat a analyzovat velké objemy dat vytvářené vašimi připojenými zařízeními a aplikacemi. Jakmile jsou shromážděna do centra událostí, dají se transformovat a ukládat data pomocí libovolného poskytovatele analýz v reálném čase nebo úložného clusteru.

Další informace najdete v tématu [Přehled služby Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-overview).

Tento kurz popisuje, jak odesílat události do centra událostí pomocí konzolové aplikace v jazyce C. Další informace o přijímání událostí, klikněte na příslušný přijímající jazyk v obsahu vlevo.

Pro absolvování tohoto kurzu potřebujete:

* Vývojové prostředí jazyka C. Tento kurz předpokládá gcc zásobníku na virtuálním počítači Azure s Linuxem s Ubuntu 14.04.
* [Microsoft Visual Studio](https://www.visualstudio.com/).
* Aktivní účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit bezplatný zkušební účet. Podrobnosti najdete v tématu [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="send-messages-to-event-hubs"></a>Zasílání zpráv do služby Event Hubs
V této části ukazuje, jak psát aplikace v jazyce C k odesílání událostí do vašeho centra událostí. Kód používá knihovnu kanálem AMQP od [projektu Apache Qpid](http://qpid.apache.org/). To je obdobou pomocí front a témat Service Bus pomocí protokolu AMQP from C, jak je znázorněno [v této ukázce](https://code.msdn.microsoft.com/Using-Apache-Qpid-Proton-C-afd76504). Další informace najdete v tématu [Qpid kanálem dokumentaci](http://qpid.apache.org/proton/index.html).

1. Z [stránky Qpid AMQP Messenger](https://qpid.apache.org/proton/messenger.html), postupujte podle pokynů k instalaci Qpid kanálem v závislosti na vašem prostředí.
2. Pro kompilaci kanálem knihovny, nainstalujte následující balíčky:
   
    ```shell
    sudo apt-get install build-essential cmake uuid-dev openssl libssl-dev
    ```
3. Stáhněte si [Qpid kanálem knihovny](http://qpid.apache.org/proton/index.html)a rozbalte ho, například:
   
    ```shell
    wget http://archive.apache.org/dist/qpid/proton/0.7/qpid-proton-0.7.tar.gz
    tar xvfz qpid-proton-0.7.tar.gz
    ```
4. Vytvořte adresář sestavení, kompilace a instalace:
   
    ```shell
    cd qpid-proton-0.7
    mkdir build
    cd build
    cmake -DCMAKE_INSTALL_PREFIX=/usr ..
    sudo make install
    ```
5. Pracovní adresář, vytvořte nový soubor s názvem **sender.c** následujícím kódem. Nezapomeňte nahradit hodnoty nebo název klíče SAS, název centra událostí a obor názvů. Musíte taky nahradit kódovaná adresou URL verzi klíče pro **SendRule** vytvořili dříve. Můžete kódování URL ho [tady](http://www.w3schools.com/tags/ref_urlencode.asp).
   
    ```c
    #include "proton/message.h"
    #include "proton/messenger.h"
   
    #include <getopt.h>
    #include <proton/util.h>
    #include <sys/time.h>
    #include <stddef.h>
    #include <stdio.h>
    #include <string.h>
    #include <unistd.h>
    #include <stdlib.h>
   
    #define check(messenger)                                                     \
      {                                                                          \
        if(pn_messenger_errno(messenger))                                        \
        {                                                                        \
          printf("check\n");                                                     \
          die(__FILE__, __LINE__, pn_error_text(pn_messenger_error(messenger))); \
        }                                                                        \
      }
   
    pn_timestamp_t time_now(void)
    {
      struct timeval now;
      if (gettimeofday(&now, NULL)) pn_fatal("gettimeofday failed\n");
      return ((pn_timestamp_t)now.tv_sec) * 1000 + (now.tv_usec / 1000);
    }  
   
    void die(const char *file, int line, const char *message)
    {
      printf("Dead\n");
      fprintf(stderr, "%s:%i: %s\n", file, line, message);
      exit(1);
    }
   
    int sendMessage(pn_messenger_t * messenger) {
        char * address = (char *) "amqps://{SAS Key Name}:{SAS key}@{namespace name}.servicebus.windows.net/{event hub name}";
        char * msgtext = (char *) "Hello from C!";
   
        pn_message_t * message;
        pn_data_t * body;
        message = pn_message();
   
        pn_message_set_address(message, address);
        pn_message_set_content_type(message, (char*) "application/octect-stream");
        pn_message_set_inferred(message, true);
   
        body = pn_message_body(message);
        pn_data_put_binary(body, pn_bytes(strlen(msgtext), msgtext));
   
        pn_messenger_put(messenger, message);
        check(messenger);
        pn_messenger_send(messenger, 1);
        check(messenger);
   
        pn_message_free(message);
    }
   
    int main(int argc, char** argv) {
        printf("Press Ctrl-C to stop the sender process\n");
   
        pn_messenger_t *messenger = pn_messenger(NULL);
        pn_messenger_set_outgoing_window(messenger, 1);
        pn_messenger_start(messenger);
   
        while(true) {
            sendMessage(messenger);
            printf("Sent message\n");
            sleep(1);
        }
   
        // release messenger resources
        pn_messenger_stop(messenger);
        pn_messenger_free(messenger);
   
        return 0;
    }
    ```
6. Zkompilujte soubor, za předpokladu, že **gcc**:
   
    ```
    gcc sender.c -o sender -lqpid-proton
    ```

    > [!NOTE]
    > Tento kód používá odchozí okna 1 přinutit zpráv si co nejdříve. Doporučuje se, že vaše aplikace pokusí zprávy dávkových chcete zvýšit propustnost. Najdete v článku [stránky Qpid AMQP Messenger](https://qpid.apache.org/proton/messenger.html) informace o tom, jak použít knihovnu Qpid kanálem v tomto a dalších prostředích a z platforem, pro které jsou k dispozici vazby (aktuálně Perl, PHP, Python a Ruby).


## <a name="next-steps"></a>Další postup
Další informace o službě Event Hubs najdete na následujících odkazech:

* [Přehled služby Event Hubs](event-hubs-what-is-event-hubs.md)
* [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)

<!-- Images. -->
[21]: ./media/event-hubs-c-ephcs-getstarted/run-csharp-ephcs1.png
[24]: ./media/event-hubs-c-ephcs-getstarted/receive-eph-c.png

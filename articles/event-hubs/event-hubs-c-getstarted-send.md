---
title: 'Úvodní příručka: Odesílání událostí pomocí C – Azure Event Hubs'
description: 'Úvodní příručka: Tento článek poskytuje návod pro vytvoření aplikace C, která odesílá události do Centra událostí Azure.'
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
ms.topic: quickstart
ms.custom: seodec18
ms.date: 11/05/2019
ms.author: shvija
ms.openlocfilehash: 5bd4bb66b7e3c3ec37724f8684105befbc9132ff
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "73720676"
---
# <a name="quickstart-send-events-to-azure-event-hubs-using-c"></a>Úvodní příručka: Odeslání událostí do Centra událostí Azure pomocí C

## <a name="introduction"></a>Úvod
Azure Event Hubs je platforma pro streamování velkých objemů dat a služba pro ingestování událostí, která je schopná přijmout a zpracovat miliony událostí za sekundu. Služba Event Hubs dokáže zpracovávat a ukládat události, data nebo telemetrické údaje produkované distribuovaným softwarem a zařízeními. Data odeslaná do centra událostí je možné transformovat a uložit pomocí libovolného poskytovatele analýz v reálném čase nebo adaptérů pro dávkové zpracování a ukládání. Podrobnější přehled služby Event Hubs najdete v tématech [Přehled služby Event Hubs](event-hubs-about.md) a [Funkce služby Event Hubs](event-hubs-features.md).

Tento kurz popisuje, jak odesílat události do centra událostí pomocí konzolové aplikace v C. 

## <a name="prerequisites"></a>Požadavky
Pro absolvování tohoto kurzu potřebujete:

* Vývojové prostředí C. Tento kurz předpokládá zásobník gcc na virtuálním počítači Azure Linux s Ubuntu 14.04.
* [Aplikace Microsoft Visual Studio](https://www.visualstudio.com/).
* **Vytvořte obor názvů Event Hubs a centrum událostí**. Na [portálu Azure](https://portal.azure.com) můžete vytvořit obor názvů typu Event Hubs a získat přihlašovací údaje pro správu, které vaše aplikace potřebuje ke komunikaci s centrem událostí. Chcete-li vytvořit obor názvů a centrum událostí, postupujte podle postupu v [tomto článku](event-hubs-create.md). Získejte hodnotu přístupového klíče pro centrum událostí podle pokynů z článku: [Získat připojovací řetězec](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Přístupový klíč v kódu, který napíšete později v tomto kurzu. Výchozí název klíče je: **RootManageSharedAccessKey**.

## <a name="write-code-to-send-messages-to-event-hubs"></a>Psaní kódu pro odesílání zpráv do centra událostí
V této části se ukazuje, jak napsat aplikaci C pro odesílání událostí do centra událostí. Kód používá knihovnu Proton AMQP z [projektu Apache Qpid](https://qpid.apache.org/). To je obdobou použití front service bus a témata s AMQP z C, jak je znázorněno [v této ukázce](https://code.msdn.microsoft.com/Using-Apache-Qpid-Proton-C-afd76504). Další informace naleznete v [dokumentaci Qpid Proton](https://qpid.apache.org/proton/index.html).

1. Na [stránce Qpid AMQP Messenger](https://qpid.apache.org/proton/messenger.html)postupujte podle pokynů k instalaci Qpid Proton, v závislosti na vašem prostředí.
2. Chcete-li zkompilovat knihovnu Proton, nainstalujte následující balíčky:
   
    ```shell
    sudo apt-get install build-essential cmake uuid-dev openssl libssl-dev
    ```
3. Stáhněte si [knihovnu Qpid Proton](https://qpid.apache.org/proton/index.html)a vyhovte ji, např.:
   
    ```shell
    wget https://archive.apache.org/dist/qpid/proton/0.7/qpid-proton-0.7.tar.gz
    tar xvfz qpid-proton-0.7.tar.gz
    ```
4. Vytvořte adresář sestavení, kompilujte a nainstalujte:
   
    ```shell
    cd qpid-proton-0.7
    mkdir build
    cd build
    cmake -DCMAKE_INSTALL_PREFIX=/usr ..
    sudo make install
    ```
5. V pracovním adresáři vytvořte nový soubor s názvem **sender.c** s následujícím kódem. Nezapomeňte nahradit hodnoty klíče/názvu sas, názvu centra událostí a oboru názvů. Musíte také nahradit url kódované verze klíče pro **SendRule** vytvořené dříve. Můžete URL-kódovat [zde](https://www.w3schools.com/tags/ref_urlencode.asp).
   
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
6. Kompilace souboru, za předpokladu, **gcc**:
   
    ```
    gcc sender.c -o sender -lqpid-proton
    ```

    > [!NOTE]
    > Tento kód používá odchozí okno 1 vynutit zprávy ven co nejdříve. Doporučujese, aby se aplikace pokusila dávkové zprávy zvýšit propustnost. Informace o tom, jak používat knihovnu Qpid Proton v tomto a dalších prostředích a na platformách, pro které jsou k dispozici vazby (v současné době Perl, PHP, Python a Ruby), najdete na [stránce Qpid AMQP Messenger.](https://qpid.apache.org/proton/messenger.html)

Spusťte aplikaci a odesílejte zprávy do centra událostí. 

Blahopřejeme! Nyní jste odeslali zprávy do centra událostí.

## <a name="next-steps"></a>Další kroky
Přečtěte si následující články:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funkce a terminologie v Azure Event Hubs](event-hubs-features.md).


<!-- Images. -->
[21]: ./media/event-hubs-c-ephcs-getstarted/run-csharp-ephcs1.png
[24]: ./media/event-hubs-c-ephcs-getstarted/receive-eph-c.png

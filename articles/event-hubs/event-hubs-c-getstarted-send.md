---
title: 'Rychlý Start: odeslání událostí pomocí jazyka C-Azure Event Hubs'
description: 'Rychlý Start: Tento článek popisuje postup vytvoření aplikace v jazyce C, která odesílá události do služby Azure Event Hubs.'
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: bfe1ca1a45f7b33d7431aed13446d8d72f79fb90
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "85315663"
---
# <a name="quickstart-send-events-to-azure-event-hubs-using-c"></a>Rychlý Start: odeslání událostí do Azure Event Hubs pomocí jazyka C

## <a name="introduction"></a>Úvod
Azure Event Hubs je platforma pro streamování velkých objemů dat a služba pro ingestování událostí, která je schopná přijmout a zpracovat miliony událostí za sekundu. Služba Event Hubs dokáže zpracovávat a ukládat události, data nebo telemetrické údaje produkované distribuovaným softwarem a zařízeními. Data odeslaná do centra událostí je možné transformovat a uložit pomocí libovolného poskytovatele analýz v reálném čase nebo adaptérů pro dávkové zpracování a ukládání. Podrobnější přehled služby Event Hubs najdete v tématech [Přehled služby Event Hubs](event-hubs-about.md) a [Funkce služby Event Hubs](event-hubs-features.md).

V tomto kurzu se dozvíte, jak odesílat události do centra událostí pomocí konzolové aplikace v jazyce C. 

## <a name="prerequisites"></a>Předpoklady
Pro absolvování tohoto kurzu potřebujete:

* Vývojové prostředí jazyka C. V tomto kurzu se předpokládá, že se zásobník RSZ na virtuálním počítači Azure Linux s Ubuntu 14,04.
* [Microsoft Visual Studio](https://www.visualstudio.com/).
* **Vytvoří obor názvů Event Hubs a centrum událostí**. Použijte [Azure Portal](https://portal.azure.com) k vytvoření oboru názvů typu Event Hubs a získání přihlašovacích údajů pro správu, které vaše aplikace potřebuje ke komunikaci s centrem událostí. Pokud chcete vytvořit obor názvů a centrum událostí, postupujte podle pokynů v [tomto článku](event-hubs-create.md). Získejte hodnotu přístupového klíče pro centrum událostí podle pokynů uvedených v článku [získání připojovacího řetězce](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Přístupovou klávesu použijete v kódu, který napíšete později v tomto kurzu. Výchozí název klíče je: **RootManageSharedAccessKey**.

## <a name="write-code-to-send-messages-to-event-hubs"></a>Psaní kódu pro odesílání zpráv do Event Hubs
V této části se dozvíte, jak napsat aplikaci v jazyce C pro odesílání událostí do centra událostí. Kód používá knihovnu Proton AMQP z [projektu Apache Qpid](https://qpid.apache.org/). To je obdobou použití Service Bus front a témat s AMQP z jazyka C, jak je znázorněno [v této ukázce](https://code.msdn.microsoft.com/Using-Apache-Qpid-Proton-C-afd76504). Další informace najdete v [dokumentaci k Qpid Proton](https://qpid.apache.org/proton/index.html).

1. Na [stránce kurýrní služby QPID AMQP](https://qpid.apache.org/proton/messenger.html)postupujte podle pokynů pro instalaci Qpid Proton v závislosti na vašem prostředí.
2. Pro zkompilování knihovny Proton nainstalujte následující balíčky:
   
    ```shell
    sudo apt-get install build-essential cmake uuid-dev openssl libssl-dev
    ```
3. Stáhněte si [knihovnu Proton Qpid](https://qpid.apache.org/proton/index.html)a extrahujte ji, např.:
   
    ```shell
    wget https://archive.apache.org/dist/qpid/proton/0.7/qpid-proton-0.7.tar.gz
    tar xvfz qpid-proton-0.7.tar.gz
    ```
4. Vytvořte adresář sestavení, zkompilujte a nainstalujte:
   
    ```shell
    cd qpid-proton-0.7
    mkdir build
    cd build
    cmake -DCMAKE_INSTALL_PREFIX=/usr ..
    sudo make install
    ```
5. V pracovním adresáři vytvořte nový soubor s názvem **sender. c** s následujícím kódem. Nezapomeňte nahradit hodnoty pro klíč/název SAS, název centra událostí a obor názvů. Také je nutné nahradit verzi klíče kódovaného adresou URL dříve vytvořeného **SendRule** . Adresu URL můžete [tady](https://www.w3schools.com/tags/ref_urlencode.asp)zakódovat.
   
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
6. Zkompilujte soubor za předpokladu **RSZ**:
   
    ```
    gcc sender.c -o sender -lqpid-proton
    ```

    > [!NOTE]
    > Tento kód používá odchozí okno 1 k tomu, aby se zprávy vynutily co nejrychleji. Doporučuje se, aby se vaše aplikace pokusila o zvýšení propustnosti zpráv. Informace o tom, jak používat knihovnu Qpid Proton v tomto a dalších prostředích a na platformách, pro které jsou k dispozici vazby (aktuálně Perl, PHP, Python a Ruby), najdete na [stránce QPID AMQP Messenger](https://qpid.apache.org/proton/messenger.html) .

Spusťte aplikaci, aby odesílala zprávy do centra událostí. 

Blahopřejeme! Nyní jste odeslali zprávy do centra událostí.

## <a name="next-steps"></a>Další kroky
Přečtěte si následující články:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funkce a terminologie v Azure Event Hubs](event-hubs-features.md).


<!-- Images. -->
[21]: ./media/event-hubs-c-ephcs-getstarted/run-csharp-ephcs1.png
[24]: ./media/event-hubs-c-ephcs-getstarted/receive-eph-c.png

---
title: Typy a popisy výstrah
description: Popisy výstrah IoT najdete v Defenderu.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 4/8/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 9ef7aa388d0f25adcafec1cb4a5b38dcfb8597a1
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2021
ms.locfileid: "107210461"
---
# <a name="alert-types-and-descriptions"></a>Typy a popisy výstrah

Tento článek popisuje všechny typy výstrah, které mohou být vygenerovány z programu Defender pro moduly IoT. Výstrahy se zobrazí v okně výstrahy, které umožňuje spravovat událost výstrahy. 

## <a name="policy-engine-alerts"></a>Výstrahy modulu zásad

Výstrahy modulu zásad popisují odchylky od zjištěného chování sítě v základní síti.

| Nadpis  | Popis | Závažnost |
|--|--|--|
| Abnormální použití adres MAC | V síti se zjistilo nové zdrojové zařízení, ale nedošlo k autorizaci. | Vedlejší |
| Software Beckhoff se změnil. | Firmware se aktualizoval na zdrojovém zařízení. Může se jednat o autorizovanou aktivitu, například o Plánovaný postup údržby. | Hlavní |
| Přihlášení k databázi selhalo. | Byl zjištěn neúspěšný pokus o přihlášení ze zdrojového zařízení na cílový server. Může to být výsledek lidské chyby, ale může to také znamenat škodlivý pokus o ohrožení serveru nebo dat. | Hlavní |
| Verze firmwaru společností Emerson ROC se změnila. | Firmware se aktualizoval na zdrojovém zařízení. Může se jednat o autorizovanou aktivitu, například o Plánovaný postup údržby. | Hlavní |
| Externí adresa v síti, která komunikuje s internetem | Zdrojové zařízení definované jako součást vaší sítě komunikuje s internetovými adresami. Zdroj nemá autorizaci ke komunikaci s internetovými adresami. | Kritické |
| Zařízení pole se nečekaně zjistilo. | V síti se zjistilo nové zdrojové zařízení, ale nedošlo k autorizaci. | Hlavní |
| Zjistila se změna firmwaru. | Firmware se aktualizoval na zdrojovém zařízení. Může se jednat o autorizovanou aktivitu, například o Plánovaný postup údržby. | Hlavní |
| Verze firmwaru se změnila. | Firmware se aktualizoval na zdrojovém zařízení. Může se jednat o autorizovanou aktivitu, například o Plánovaný postup údržby. | Hlavní |
| Foxboro I/A neautorizovanou operaci | Byly zjištěny nové parametry provozu. Tato kombinace parametrů nebyla autorizována jako zjištěná komunikace ve vaší síti. Následující kombinace je neoprávněná. | Hlavní |
| Přihlášení k serveru FTP se nezdařilo | Byl zjištěn neúspěšný pokus o přihlášení ze zdrojového zařízení na cílový server. Může to být výsledek lidské chyby, ale může to také znamenat škodlivý pokus o ohrožení serveru nebo dat. | Hlavní |
| Kód funkce vyvolal neoprávněnou výjimku. | Zdrojové zařízení (podřízené) vrátilo výjimku do cílového zařízení (Master). | Hlavní |
| Nastavení typu zprávy o Hus | Nastavení zprávy (identifikované ID protokolu) se změnila na zdrojovém zařízení. | Upozornění |
| Verze firmwaru Honeywell se změnila. | Firmware se aktualizoval na zdrojovém zařízení. Může se jednat o autorizovanou aktivitu, například o Plánovaný postup údržby. | Hlavní |
| Neplatná komunikace HTTP | Byly zjištěny nové parametry provozu. Tato kombinace parametrů nebyla autorizována jako zjištěná komunikace ve vaší síti. Následující kombinace je neoprávněná. | Hlavní |
| Byl zjištěn přístup k Internetu | Zdrojové zařízení definované jako součást vaší sítě komunikuje s internetovými adresami. Zdroj nemá autorizaci ke komunikaci s internetovými adresami. | Hlavní |
| Verze firmwaru Mitsubishi se změnila. | Firmware se aktualizoval na zdrojovém zařízení. Může se jednat o autorizovanou aktivitu, například o Plánovaný postup údržby. | Hlavní |
| Porušení rozsahu adres Modbus | Hlavní zařízení požadovalo přístup k nové adrese podřízené paměti. | Hlavní |
| Verze firmwaru Modbus se změnila. | Firmware se aktualizoval na zdrojovém zařízení. Může se jednat o autorizovanou aktivitu, například o Plánovaný postup údržby. | Hlavní |
| Byla zjištěna nová aktivita – třída CIP | Byly zjištěny nové parametry provozu. Tato kombinace parametrů nebyla autorizována jako zjištěná komunikace ve vaší síti. Následující kombinace je neoprávněná. | Hlavní |
| Zjištěna nová aktivita – služba třídy CIP | Byly zjištěny nové parametry provozu. Tato kombinace parametrů nebyla autorizována jako zjištěná komunikace ve vaší síti. Následující kombinace je neoprávněná. | Hlavní |
| Zjištěna nová aktivita – příkaz CIP PCCC | Byly zjištěny nové parametry provozu. Tato kombinace parametrů nebyla autorizována jako zjištěná komunikace ve vaší síti. Následující kombinace je neoprávněná. | Hlavní |
| Zjištěna nová aktivita – symbol CIP | Byly zjištěny nové parametry provozu. Tato kombinace parametrů nebyla autorizována jako zjištěná komunikace ve vaší síti. Následující kombinace je neoprávněná. | Hlavní |
| Zjištěna nová aktivita – vstupně-výstupní připojení sítě EtherNet/IP | Byly zjištěny nové parametry provozu. Tato kombinace parametrů nebyla autorizována jako zjištěná komunikace ve vaší síti. Následující kombinace je neoprávněná. | Hlavní |
| Zjištěna nová aktivita – příkaz EtherNet/IP Protocol | Byly zjištěny nové parametry provozu. Tato kombinace parametrů nebyla autorizována jako zjištěná komunikace ve vaší síti. Následující kombinace je neoprávněná. | Hlavní |
| Zjištěna nová aktivita – kód zprávy GSM | Byly zjištěny nové parametry provozu. Tato kombinace parametrů nebyla autorizována jako zjištěná komunikace ve vaší síti. Následující kombinace je neoprávněná. | Hlavní |
| Zjištěna nová aktivita – kódy příkazů LonTalk | Byly zjištěny nové parametry provozu. Tato kombinace parametrů nebyla autorizována jako zjištěná komunikace ve vaší síti. Následující kombinace je neoprávněná. | Hlavní |
| Nové zjišťování portů | Byly zjištěny nové parametry provozu. Tato kombinace parametrů nebyla autorizována jako zjištěná komunikace ve vaší síti. Následující kombinace je neoprávněná. | Upozornění |
| Zjištěna nová aktivita – LonTalk síťová proměnná | Byly zjištěny nové parametry provozu. Tato kombinace parametrů nebyla autorizována jako zjištěná komunikace ve vaší síti. Následující kombinace je neoprávněná. | Hlavní |
| Zjištěna nová aktivita – žádost Ovation data | Byly zjištěny nové parametry provozu. Tato kombinace parametrů nebyla autorizována jako zjištěná komunikace ve vaší síti. Následující kombinace je neoprávněná. | Hlavní |
| Byla zjištěna nová aktivita – příkaz pro čtení a zápis (skupina indexu AMS). | Byly zjištěny nové parametry provozu. Tato kombinace parametrů nebyla autorizována jako zjištěná komunikace ve vaší síti. Následující kombinace je neoprávněná. | Hlavní |
| Byla zjištěna nová aktivita – příkaz pro čtení a zápis (posun indexu AMS). | Byly zjištěny nové parametry provozu. Tato kombinace parametrů nebyla autorizována jako zjištěná komunikace ve vaší síti. Následující kombinace je neoprávněná. | Hlavní |
| Zjištěna nová aktivita – neautorizovaný typ zprávy DeltaV | Byly zjištěny nové parametry provozu. Tato kombinace parametrů nebyla autorizována jako zjištěná komunikace ve vaší síti. Následující kombinace je neoprávněná. | Hlavní |
| Zjistila se nová aktivita – Neautorizovaná operace DeltaV ROC | Byly zjištěny nové parametry provozu. Tato kombinace parametrů nebyla autorizována jako zjištěná komunikace ve vaší síti. Následující kombinace je neoprávněná. | Hlavní |
| Zjištěna nová aktivita – neautorizovaný typ zprávy RPC | Byly zjištěny nové parametry provozu. Tato kombinace parametrů nebyla autorizována jako zjištěná komunikace ve vaší síti. Následující kombinace je neoprávněná. | Hlavní |
| Zjištěna nová aktivita – neautorizované volání procedury RPC | Byly zjištěny nové parametry provozu. Tato kombinace parametrů nebyla autorizována jako zjištěná komunikace ve vaší síti. Následující kombinace je neoprávněná. | Hlavní |
| Zjistila se nová aktivita – použijte příkaz protokolu AMS. | Byly zjištěny nové parametry provozu. Tato kombinace parametrů nebyla autorizována jako zjištěná komunikace ve vaší síti. Následující kombinace je neoprávněná. | Hlavní |
| Zjištěna nová aktivita – použití příkazu Siemens SICAM | Byly zjištěny nové parametry provozu. Tato kombinace parametrů nebyla autorizována jako zjištěná komunikace ve vaší síti. Následující kombinace je neoprávněná. | Hlavní |
| Zjištěna nová aktivita – pomocí příkazu protokolu Suitelink | Byly zjištěny nové parametry provozu. Tato kombinace parametrů nebyla autorizována jako zjištěná komunikace ve vaší síti. Následující kombinace je neoprávněná. | Hlavní |
| Zjištěna nová aktivita – použití relací protokolu Suitelink | Byly zjištěny nové parametry provozu. Tato kombinace parametrů nebyla autorizována jako zjištěná komunikace ve vaší síti. Následující kombinace je neoprávněná. | Hlavní |
| Zjištěna nová aktivita – použití příkazu Yokogawa VNetIP | Byly zjištěny nové parametry provozu. Tato kombinace parametrů nebyla autorizována jako zjištěná komunikace ve vaší síti. Následující kombinace je neoprávněná. | Hlavní |
| Zjistil se nový Asset. | V síti se zjistilo nové zdrojové zařízení, ale nedošlo k autorizaci. | Hlavní |
| Nová konfigurace zařízení LLDP | V síti se zjistilo nové zdrojové zařízení, ale nedošlo k autorizaci. | Hlavní |
| Nové zjišťování portů | Byly zjištěny nové parametry provozu. Tato kombinace parametrů nebyla autorizována jako zjištěná komunikace ve vaší síti. Následující kombinace je neoprávněná. | Upozornění |
| Neautorizovaný příkaz Omron PLOUTVÍ | Byly zjištěny nové parametry provozu. Tato kombinace parametrů nebyla autorizována jako zjištěná komunikace ve vaší síti. Následující kombinace je neoprávněná. | Hlavní |
| S7 a firmware pro PLC se změnil. | Firmware se aktualizoval na zdrojovém zařízení. Může se jednat o autorizovanou aktivitu, například o Plánovaný postup údržby. | Hlavní |
| Ukázka hodnot – nastavení typu zprávy | Nastavení zprávy (identifikované ID protokolu) se změnila na zdrojovém zařízení. | Upozornění |
| Podezření na neplatnou kontrolu integrity | Byla zjištěna kontrola na zdrojovém zařízení DNP3 (stanice). Tato kontrola nebyla autorizována jako zjištěná přenosová data ve vaší síti. | Hlavní |
| Příkaz Toshiba Computer Link Neautorizováno | Byly zjištěny nové parametry provozu. Tato kombinace parametrů nebyla autorizována jako zjištěná komunikace ve vaší síti. Následující kombinace je neoprávněná. | Vedlejší |
| Neautorizovaná operace souboru Totalflow ABB | Byly zjištěny nové parametry provozu. Tato kombinace parametrů nebyla autorizována jako zjištěná komunikace ve vaší síti. Následující kombinace je neoprávněná. | Hlavní |
| Neautorizovaná operace registru Totalflow ABB | Byly zjištěny nové parametry provozu. Tato kombinace parametrů nebyla autorizována jako zjištěná komunikace ve vaší síti. Následující kombinace je neoprávněná. | Hlavní |
| Neoprávněný přístup k datovému bloku S7 společnosti Siemens | Zdrojové zařízení se pokusilo o přístup k prostředku na jiném zařízení. Pokus o přístup k tomuto prostředku v těchto dvou zařízeních se neautorizovaný jako zjištěná komunikace ve vaší síti. | Upozornění |
| Neoprávněný přístup k objektu Siemens S7 plus | Byly zjištěny nové parametry provozu. Tato kombinace parametrů nebyla autorizována jako zjištěná komunikace ve vaší síti. Následující kombinace je neoprávněná. | Hlavní |
| Neoprávněný přístup k značce Wonderware | Zdrojové zařízení se pokusilo o přístup k prostředku na jiném zařízení. Pokus o přístup k tomuto prostředku v těchto dvou zařízeních se neautorizovaný jako zjištěná komunikace ve vaší síti. | Hlavní |
| Neautorizovaný přístup k objektům BACNet | Byly zjištěny nové parametry provozu. Tato kombinace parametrů nebyla autorizována jako zjištěná komunikace ve vaší síti. Následující kombinace je neoprávněná. | Hlavní |
| Neautorizovaná trasa BACNet | Byly zjištěny nové parametry provozu. Tato kombinace parametrů nebyla autorizována jako zjištěná komunikace ve vaší síti. Následující kombinace je neoprávněná. | Hlavní |
| Neoprávněné přihlášení k databázi | Byl zjištěn pokus o přihlášení mezi zdrojovým klientem a cílovým serverem. Komunikace mezi těmito zařízeními nebyla ověřena jako zjištěná komunikace ve vaší síti. | Hlavní |
| Neautorizovaná databázová operace | Byly zjištěny nové parametry provozu. Tato kombinace parametrů nebyla autorizována jako zjištěná komunikace ve vaší síti. Následující kombinace je neoprávněná. | Hlavní |
| Neautorizovaná operace společností Emerson ROC | Byly zjištěny nové parametry provozu. Tato kombinace parametrů nebyla autorizována jako zjištěná komunikace ve vaší síti. Následující kombinace je neoprávněná. | Hlavní |
| Neautorizovaný přístup k souboru GE SRTP | Byly zjištěny nové parametry provozu. Tato kombinace parametrů nebyla autorizována jako zjištěná komunikace ve vaší síti. Následující kombinace je neoprávněná. | Hlavní |
| Neautorizovaný příkaz GE SRTP Protocol | Byly zjištěny nové parametry provozu. Tato kombinace parametrů nebyla autorizována jako zjištěná komunikace ve vaší síti. Následující kombinace je neoprávněná. | Hlavní |
| Neoprávněná operace GE SRTP systémová paměť | Byly zjištěny nové parametry provozu. Tato kombinace parametrů nebyla autorizována jako zjištěná komunikace ve vaší síti. Následující kombinace je neoprávněná. | Hlavní |
| Neautorizovaná aktivita HTTP | Byly zjištěny nové parametry provozu. Tato kombinace parametrů nebyla autorizována jako zjištěná komunikace ve vaší síti. Následující kombinace je neoprávněná. | Hlavní |
| Neautorizovaný server HTTP | Na zdrojovém zařízení byla zjištěna neoprávněná aplikace. Aplikace nebyla autorizována jako zjištěná aplikace ve vaší síti. | Hlavní |
| Neautorizovaná akce HTTP SOAP | Byly zjištěny nové parametry provozu. Tato kombinace parametrů nebyla autorizována jako zjištěná komunikace ve vaší síti. Následující kombinace je neoprávněná. | Hlavní |
| Neautorizovaný uživatelský agent HTTP | Na zdrojovém zařízení byla zjištěna neoprávněná aplikace. Aplikace nebyla autorizována jako zjištěná aplikace ve vaší síti. | Hlavní |
| Bylo zjištěno neoprávněné připojení k Internetu. | Zdrojové zařízení definované jako součást vaší sítě komunikuje s internetovými adresami. Zdroj nemá autorizaci ke komunikaci s internetovými adresami. | Kritické |
| Neautorizovaný příkaz Mitsubishi MELSEC | Byly zjištěny nové parametry provozu. Tato kombinace parametrů nebyla autorizována jako zjištěná komunikace ve vaší síti. Následující kombinace je neoprávněná. | Hlavní |
| Neautorizovaný přístup k programu MMS | Zdrojové zařízení se pokusilo o přístup k prostředku na jiném zařízení. Pokus o přístup k tomuto prostředku v těchto dvou zařízeních se neautorizovaný jako zjištěná komunikace ve vaší síti. | Hlavní |
| Neautorizovaná Služba MMS | Byly zjištěny nové parametry provozu. Tato kombinace parametrů nebyla autorizována jako zjištěná komunikace ve vaší síti. Následující kombinace je neoprávněná. | Hlavní |
| Neautorizované připojení vícesměrového vysílání nebo všesměrového vysílání | Bylo zjištěno připojení vícesměrového vysílání nebo všesměrového vysílání mezi zdrojovým zařízením a dalšími zařízeními. Komunikace vícesměrového vysílání nebo všesměrového vysílání není autorizována. | Kritické |
| Neautorizovaný dotaz na název | Byly zjištěny nové parametry provozu. Tato kombinace parametrů nebyla autorizována jako zjištěná komunikace ve vaší síti. Následující kombinace je neoprávněná. | Hlavní |
| Neautorizovaná aktivita OPC UA | Byly zjištěny nové parametry provozu. Tato kombinace parametrů nebyla autorizována jako zjištěná komunikace ve vaší síti. Následující kombinace je neoprávněná. | Hlavní |
| Neautorizovaný požadavek nebo odpověď OPC UA | Byly zjištěny nové parametry provozu. Tato kombinace parametrů nebyla autorizována jako zjištěná komunikace ve vaší síti. Následující kombinace je neoprávněná. | Hlavní |
| Uživatelem definovaná pravidla zjistila neoprávněnou operaci. | Mezi dvěma zařízeními byl zjištěn přenos. Tato aktivita je Neautorizovaná na základě vlastního pravidla výstrahy definovaného uživatelem. | Hlavní |
| Neoprávněné čtení konfigurace PLC | Zdrojové zařízení není definováno jako programové zařízení, ale na cílovém řadiči se prováděla operace čtení/zápisu. Změny programování by měly být provedeny pouze programováním zařízení. Na tomto zařízení je možná nainstalovaná aplikace programování. | Upozornění |
| Neoprávněný zápis pro konfiguraci PLC | Zdrojové zařízení odeslalo příkaz pro čtení/zápis programu cílového kontroleru. Tato aktivita se dřív nezobrazila. | Hlavní |
| Neautorizovaný nahrávání programu PLC | Zdrojové zařízení odeslalo příkaz pro čtení/zápis programu cílového kontroleru. Tato aktivita se dřív nezobrazila. | Hlavní |
| Neoprávněné programování PLC | Zdrojové zařízení není definováno jako programové zařízení, ale na cílovém řadiči se prováděla operace čtení/zápisu. Změny programování by měly být provedeny pouze programováním zařízení. Na tomto zařízení je možná nainstalovaná aplikace programování. | Kritické |
| Neautorizovaný typ rámce projemného typu | Byly zjištěny nové parametry provozu. Tato kombinace parametrů nebyla autorizována jako zjištěná komunikace ve vaší síti. Následující kombinace je neoprávněná. | Hlavní |
| Neautorizovaný SAIA S – příkaz sběrnice | Byly zjištěny nové parametry provozu. Tato kombinace parametrů nebyla autorizována jako zjištěná komunikace ve vaší síti. Následující kombinace je neoprávněná. | Hlavní |
| Neautorizované provádění funkce Control S7 společnosti Siemens | Byly zjištěny nové parametry provozu. Tato kombinace parametrů nebyla autorizována jako zjištěná komunikace ve vaší síti. Následující kombinace je neoprávněná. | Hlavní |
| Neautorizované provádění uživatelsky definované funkce S7 Siemens | Byly zjištěny nové parametry provozu. Tato kombinace parametrů nebyla autorizována jako zjištěná komunikace ve vaší síti. Následující kombinace je neoprávněná. | Hlavní |
| Neautorizovaný program Siemens S7 plus přístup k bloku | Byly zjištěny nové parametry provozu. Tato kombinace parametrů nebyla autorizována jako zjištěná komunikace ve vaší síti. Následující kombinace je neoprávněná. | Hlavní |
| Neautorizovaná operace S7 Plus společnosti Siemens | Byly zjištěny nové parametry provozu. Tato kombinace parametrů nebyla autorizována jako zjištěná komunikace ve vaší síti. Následující kombinace je neoprávněná. | Hlavní |
| Neoprávněné přihlášení SMB | Byl zjištěn pokus o přihlášení mezi zdrojovým klientem a cílovým serverem. Komunikace mezi těmito zařízeními nebyla ověřena jako zjištěná komunikace ve vaší síti. | Hlavní |
| Neautorizovaná operace protokolu SNMP | Byly zjištěny nové parametry provozu. Tato kombinace parametrů nebyla autorizována jako zjištěná komunikace ve vaší síti. Následující kombinace je neoprávněná. | Hlavní |
| Neautorizovaný přístup přes SSH | Byly zjištěny nové parametry provozu. Tato kombinace parametrů nebyla autorizována jako zjištěná komunikace ve vaší síti. Následující kombinace je neoprávněná. | Hlavní |
| Neautorizovaný proces systému Windows | Na zdrojovém zařízení byla zjištěna neoprávněná aplikace. Aplikace nebyla autorizována jako zjištěná aplikace ve vaší síti. | Hlavní |
| Neautorizovaná služba systému Windows | Na zdrojovém zařízení byla zjištěna neoprávněná aplikace. Aplikace nebyla autorizována jako zjištěná aplikace ve vaší síti. | Hlavní |
| Uživatelem definovaná pravidla zjistila neoprávněnou operaci. | Byly zjištěny nové parametry provozu. Tato kombinace parametrů porušuje pravidlo definované uživatelem. | Hlavní |
| Nepovolené Schneider elektrické rozšíření Modbus | Byly zjištěny nové parametry provozu. Tato kombinace parametrů nebyla autorizována jako zjištěná komunikace ve vaší síti. Následující kombinace je neoprávněná. | Hlavní |
| Nepovolené použití typů ASDU | Byly zjištěny nové parametry provozu. Tato kombinace parametrů nebyla autorizována jako zjištěná komunikace ve vaší síti. Následující kombinace je neoprávněná. | Hlavní |
| Nepovolené použití kódu funkce DNP3 | Byly zjištěny nové parametry provozu. Tato kombinace parametrů nebyla autorizována jako zjištěná komunikace ve vaší síti. Následující kombinace je neoprávněná. | Hlavní |
| Nepovolené použití interní indikace (PROFILACE) | Zdrojové zařízení DNP3 (stanoviště) oznámilo interní indikaci (PROFILACE), které Neautorizováno jako zjištěné přenosy ve vaší síti. | Hlavní |
| Nepovolené použití kódu funkce Modbus | Byly zjištěny nové parametry provozu. Tato kombinace parametrů nebyla autorizována jako zjištěná komunikace ve vaší síti. Následující kombinace je neoprávněná. | Hlavní |

## <a name="anomaly-engine-alerts"></a>Výstrahy modulu anomálií

| Nadpis | Popis | Závažnost |
|--|--|--|
| Neobvyklý vzor výjimky na podřízeném | Na zdrojovém zařízení bylo zjištěno nadměrné množství chyb. To může být výsledkem provozního problému. | Vedlejší |
| Abnormální délka hlavičky protokolu HTTP | Zdrojové zařízení odeslalo neobvyklou zprávu. To může znamenat pokus o útok na cílové zařízení. | Kritické |
| Neobvyklý počet parametrů v hlavičce protokolu HTTP | Zdrojové zařízení odeslalo neobvyklou zprávu. To může znamenat pokus o útok na cílové zařízení. | Kritické |
| Neobvyklé pravidelné chování v komunikačním kanálu | Byla zjištěna změna frekvence komunikace mezi zdrojovým a cílovým zařízením. | Vedlejší |
| Abnormální ukončení aplikací | Na zdrojovém zařízení bylo zjištěno nadměrné množství příkazů Stop. Může to být výsledkem provozního problému nebo pokusu o manipulaci se zařízením. | Hlavní |
| Abnormální šířka pásma provozu | Na kanálu byla zjištěna abnormální šířka pásma. Šířka pásma se zdá být podstatně nižší nebo vyšší než dříve zjištěná. Podrobnosti můžete pracovat s pomůckou celkové šířky pásma. | Upozornění |
| Abnormální šířka pásma provozu mezi zařízeními | Na kanálu byla zjištěna abnormální šířka pásma. Šířka pásma se zdá být podstatně nižší nebo vyšší než dříve zjištěná. Podrobnosti můžete pracovat s pomůckou celkové šířky pásma. | Upozornění |
| Zjistila se kontrola adresy. | Při kontrole síťových zařízení bylo zjištěno zdrojové zařízení. Toto zařízení není autorizované jako síťové skenovací zařízení. | Kritické |
| Zjistila se kontrola adresy protokolu ARP. | Zjistilo se, že se na zdrojovém zařízení kontroluje síťová zařízení pomocí protokolu ARP (Address Resolution Protocol). Tato adresa zařízení nebyla autorizována jako platná adresa pro skenování protokolu ARP. | Kritické |
| Zjistila se kontrola adresy protokolu ARP. | Zjistilo se, že se na zdrojovém zařízení kontroluje síťová zařízení pomocí protokolu ARP (Address Resolution Protocol). Tato adresa zařízení nebyla autorizována jako platná adresa pro skenování protokolu ARP. | Kritické |
| Falšování protokolu ARP | V síti bylo zjištěno neobvyklé množství paketů. To může znamenat útok, například falšování protokolu ARP nebo útok zahlcením protokolem ICMP. | Upozornění |
| Nadměrné pokusy o přihlášení | Zdrojovému zařízení se zjistilo, že se na cílovém serveru prováděly nadměrné pokusy o přihlášení. Může se jednat o útok hrubou silou. Server může být ohrožen škodlivým objektem actor. | Kritické |
| Nadměrný počet relací | Zdrojovému zařízení se zjistilo, že se na cílovém serveru prováděly nadměrné pokusy o přihlášení. Může se jednat o útok hrubou silou. Server může být ohrožen škodlivým objektem actor. | Kritické |
| Nadměrná rychlost restartování z dokovací stanice | Na zdrojovém zařízení bylo zjištěno nadměrné množství příkazů k restartování. Může to být výsledkem provozního problému nebo pokusu o manipulaci se zařízením. | Hlavní |
| Nadměrné pokusy o přihlášení SMB | Zdrojovému zařízení se zjistilo, že se na cílovém serveru prováděly nadměrné pokusy o přihlášení. Může se jednat o útok hrubou silou. Server může být ohrožen škodlivým objektem actor. | Kritické |
| Zahlcení ICMP | V síti bylo zjištěno neobvyklé množství paketů. To může znamenat útok, například falšování protokolu ARP nebo útok zahlcením protokolem ICMP. | Upozornění |
| Neplatný obsah hlavičky protokolu HTTP | Zdrojové zařízení iniciovalo neplatnou žádost. | Kritické |
| Neaktivní komunikační kanál | Komunikační kanál mezi dvěma zařízeními byl během období, kdy se aktivita obvykle zobrazuje, neaktivní. To může znamenat, že program, který vygeneroval tento provoz, byl změněn nebo program nebude k dispozici. Doporučuje se zkontrolovat konfiguraci nainstalovaného programu a ověřit, jestli je správně nakonfigurované. | Upozornění |
| Zjistila se kontrola adresy po dlouhou dobu. | Při kontrole síťových zařízení bylo zjištěno zdrojové zařízení. Toto zařízení není autorizované jako síťové skenovací zařízení. | Kritické |
| Byl zjištěn pokus o odhad hesla | Zdrojovému zařízení se zjistilo, že se na cílovém serveru prováděly nadměrné pokusy o přihlášení. Může se jednat o útok hrubou silou. Server může být ohrožen škodlivým objektem actor. | Kritické |
| Zjistila se kontrola PLC. | Při kontrole síťových zařízení bylo zjištěno zdrojové zařízení. Toto zařízení není autorizované jako síťové skenovací zařízení. | Kritické |
| Zjistila se kontrola portů. | Při kontrole síťových zařízení bylo zjištěno zdrojové zařízení. Toto zařízení není autorizované jako síťové skenovací zařízení. | Kritické |
| Neočekávaná délka zprávy | Zdrojové zařízení odeslalo neobvyklou zprávu. To může znamenat pokus o útok na cílové zařízení. | Kritické |
| Neočekávaný provoz pro standardní port | V zařízení se zjistil provoz s použitím portu rezervovaného pro jiný protokol. | Hlavní |

## <a name="protocol-violation-engine-alerts"></a>Výstrahy modulu porušení protokolu

| Nadpis | Popis | Závažnost |
|--|--|--|
| Nadměrné chybné poškození paketů v jedné relaci | Neobvyklý počet poškozených paketů odeslaných ze zdrojového zařízení do cílového zařízení. To může znamenat chybnou komunikaci nebo pokus o manipulaci s cílovým zařízením. | Hlavní |
| Aktualizace firmwaru | Zdrojové zařízení odeslalo příkaz pro aktualizaci firmwaru na cílovém zařízení. Ověřte, že je platné poslední programování, konfigurace a upgrady firmwaru na cílovém zařízení. | Upozornění |
| Kód funkce není podporován vydanou službou. | Cílové zařízení přijalo neplatný požadavek. | Hlavní |
| Neplatná zpráva BACNet | Zdrojové zařízení iniciovalo neplatnou žádost. | Hlavní |
| Neplatný pokus o připojení na portu 0 | Zdrojové zařízení se pokusilo připojit k cílovému zařízení na portu číslo nula (0). Pro TCP je port 0 rezervovaný a nedá se použít. Pro protokol UDP je port nepovinný a hodnota 0 znamená žádný port. V systému není obvykle žádná služba, která naslouchá na portu 0. Tato událost může znamenat pokus o útok na cílové zařízení nebo o tom, že aplikace byla nesprávně naprogramována. | Vedlejší |
| Neplatná operace DNP3 | Zdrojové zařízení iniciovalo neplatnou žádost. | Hlavní |
| Neplatná operace MODBUS (Vyvolá se výjimka z hlavní větve) | Zdrojové zařízení iniciovalo neplatnou žádost. | Hlavní |
| Neplatná operace MODBUS (kód funkce nula) | Zdrojové zařízení iniciovalo neplatnou žádost. | Hlavní |
| Neplatná verze protokolu | Zdrojové zařízení iniciovalo neplatnou žádost. | Hlavní |
| Nesprávný parametr odeslaný do trakčního přenosu | Cílové zařízení přijalo neplatný požadavek. | Hlavní |
| Zahájení zastaralého kódu funkce (inicializovat data) | Zdrojové zařízení iniciovalo neplatnou žádost. | Vedlejší |
| Zahájení zastaralého kódu funkce (Uložit konfiguraci) | Zdrojové zařízení iniciovalo neplatnou žádost. | Vedlejší |
| Hlavní server požádal o potvrzení vrstvy aplikace | Zdrojové zařízení iniciovalo neplatnou žádost. | Upozornění |
| Výjimka Modbus | Zdrojové zařízení (podřízené) vrátilo výjimku do cílového zařízení (Master). | Hlavní |
| Podřízené zařízení přijalo neplatný typ ASDU. | Cílové zařízení přijalo neplatný požadavek. | Hlavní |
| Podřízené zařízení přijalo neplatnou příčinu příkazu pro přenos. | Cílové zařízení přijalo neplatný požadavek. | Hlavní |
| Podřízené zařízení přijalo neplatnou společnou adresu. | Cílové zařízení přijalo neplatný požadavek. | Hlavní |
| Podřízené zařízení přijalo neplatný parametr datové adresy. | Cílové zařízení přijalo neplatný požadavek. | Hlavní |
| Podřízené zařízení přijalo neplatný parametr hodnoty dat. | Cílové zařízení přijalo neplatný požadavek. | Hlavní |
| Podřízené zařízení přijalo neplatný kód funkce. | Cílové zařízení přijalo neplatný požadavek. | Hlavní |
| Podřízené zařízení přijalo neplatnou adresu informačního objektu. | Cílové zařízení přijalo neplatný požadavek. | Hlavní |
| Neznámý objekt odeslaný do vyslané stanice | Cílové zařízení přijalo neplatný požadavek. | Hlavní |
| Použití rezervovaného kódu funkce | Zdrojové zařízení iniciovalo neplatnou žádost. | Hlavní |
| Použití nesprávného formátování využíváním | Zdrojové zařízení iniciovalo neplatnou žádost. | Upozornění |
| Použití vyhrazených příznaků stavu (PROFILACE) | Zdrojové zařízení DNP3 (odstanice) používalo vyhrazený vnitřní indikátor 2,6. Doporučuje se ověřit konfiguraci zařízení. | Upozornění |

## <a name="malware-engine-alerts"></a>Výstrahy malwarového modulu

| Nadpis | Popis| Závažnost |
|--|--|--|
| Pokus o připojení ke známému škodlivému IP adrese | Zjistila se podezřelá síťová aktivita. Tato aktivita může být přidružená k útoku zneužijecí metodu používanou známým malwarem. | Hlavní |
| Neplatná zpráva protokolu SMB (DoublePulsar zadní vrátka implantátu) | Zjistila se podezřelá síťová aktivita. Tato aktivita může být přidružená k útoku zneužijecí metodu používanou známým malwarem. | Kritické |
| Škodlivá žádost o název domény | Zjistila se podezřelá síťová aktivita. Tato aktivita může být přidružená k útoku zneužijecí metodu používanou známým malwarem. | Hlavní |
| Zjištěn soubor malwarového testu – příkaz EICAR AV úspěch | V provozu mezi dvěma zařízeními byl zjištěn testovací soubor aplikace EICAR AV. Soubor není malwarem. Slouží k potvrzení, že je antivirový software nainstalován správně. předvádí, co se stane, když se najde virus, a při nalezení viru ověřte interní postupy a reakce. Antivirový software by měl detekovat EICAR, jako by se jednalo o skutečný virus. | Hlavní |
| Podezření na malware v červech Conficker | Zjistila se podezřelá síťová aktivita. Tato aktivita může být přidružená k útoku zneužijecí metodu používanou známým malwarem. | Hlavní |
| Podezření na útok s cílem odepření služeb | Zdrojové zařízení se pokusilo o inicializaci nadměrného počtu nových připojení k cílovému zařízení. Může se jednat o útok DoS (Denial of Service) na cílové zařízení a může přerušit funkčnost zařízení, ovlivnit výkon a dostupnost služby nebo způsobit neopravitelné chyby. | Kritické |
| Podezření na škodlivou aktivitu | Zjistila se podezřelá síťová aktivita. Tato aktivita může být přidružená k útoku zneužijecí metodu používanou známým malwarem. | Hlavní |
| Podezření na škodlivou aktivitu (BlackEnergy) | Zjistila se podezřelá síťová aktivita. Tato aktivita může být přidružená k útoku zneužijecí metodu používanou známým malwarem. | Kritické |
| Podezření na škodlivou aktivitu (DarkComet) | Zjistila se podezřelá síťová aktivita. Tato aktivita může být přidružená k útoku zneužijecí metodu používanou známým malwarem. | Kritické |
| Podezření na škodlivou aktivitu (DuQu) | Zjistila se podezřelá síťová aktivita. Tato aktivita může být přidružená k útoku zneužijecí metodu používanou známým malwarem. | Kritické |
| Podezření na škodlivou aktivitu (plamen) | Zjistila se podezřelá síťová aktivita. Tato aktivita může být přidružená k útoku zneužijecí metodu používanou známým malwarem. | Kritické |
| Podezření na škodlivou aktivitu (Havex) | Zjistila se podezřelá síťová aktivita. Tato aktivita může být přidružená k útoku zneužijecí metodu používanou známým malwarem. | Kritické |
| Podezření na škodlivou aktivitu (Karagany) | Zjistila se podezřelá síťová aktivita. Tato aktivita může být přidružená k útoku zneužijecí metodu používanou známým malwarem. | Kritické |
| Podezření na škodlivou aktivitu (indikátory) | Zjistila se podezřelá síťová aktivita. Tato aktivita může být přidružená k útoku zneužijecí metodu používanou známým malwarem. | Kritické |
| Podezření na škodlivou aktivitu (pojmenování dotazů) | Zjistila se podezřelá síťová aktivita. Tato aktivita může být přidružená k útoku zneužijecí metodu používanou známým malwarem. | Hlavní |
| Podezření na škodlivou aktivitu (poškození Ivy) | Zjistila se podezřelá síťová aktivita. Tato aktivita může být přidružená k útoku zneužijecí metodu používanou známým malwarem. | Kritické |
| Podezření na škodlivou aktivitu (Regin) | Zjistila se podezřelá síťová aktivita. Tato aktivita může být přidružená k útoku zneužijecí metodu používanou známým malwarem. | Kritické |
| Podezření na škodlivou aktivitu (Stuxnet) | Zjistila se podezřelá síťová aktivita. Tato aktivita může být přidružená k útoku zneužijecí metodu používanou známým malwarem. | Kritické |
| Podezření na škodlivou aktivitu (WannaCry) | Zjistila se podezřelá síťová aktivita. Tato aktivita může být přidružená k útoku zneužijecí metodu používanou známým malwarem. | Hlavní |
| Bylo zjištěno podezření na NotPetya malware – neplatné parametry protokolu SMB. | Zjistila se podezřelá síťová aktivita. Tato aktivita může být přidružená k útoku zneužijecí metodu používanou známým malwarem. | Kritické |
| Bylo zjištěno podezření na nepovolenou transakci SMB NotPetya malware. | Zjistila se podezřelá síťová aktivita. Tato aktivita může být přidružená k útoku zneužijecí metodu používanou známým malwarem. | Kritické |
| Podezření na vzdálené spuštění kódu pomocí PsExec | Zjistila se podezřelá síťová aktivita. Tato aktivita může být přidružená k útoku zneužijecí metodu používanou známým malwarem. | Hlavní |
| Podezření na vzdálenou správu služeb systému Windows | Zjistila se podezřelá síťová aktivita. Tato aktivita může být přidružená k útoku zneužijecí metodu používanou známým malwarem. | Hlavní |
| Na koncovém bodu byl zjištěn podezřelý spustitelný soubor | Zjistila se podezřelá síťová aktivita. Tato aktivita může být přidružená k útoku zneužijecí metodu používanou známým malwarem. | Hlavní |
| Zjištěn podezřelý provoz | Zjistila se podezřelá síťová aktivita. Tato aktivita může být přidružená k útoku zneužijecí metodu používanou známým malwarem. | Kritické |

## <a name="operational-engine-alerts"></a>Výstrahy provozního modulu

| Nadpis | Popis | Závažnost |
|--|--|--|
| Odeslal se příkaz S7 stop PLC. | Zdrojové zařízení odeslalo do cílového kontroleru příkaz k zastavení. Kontroler přestane pracovat, dokud se neodešle spouštěcí příkaz. | Upozornění |
| Operace BACNet selhala. | Server vrátil kód chyby. To indikuje chybu serveru nebo neplatný požadavek klientem. | Hlavní |
| Chybný stav zařízení MMS | Virtuální výrobní zařízení MMS (VMD) odeslalo stavovou zprávu. Zpráva indikuje, že server nemusí být správně nakonfigurovaný, částečně funkční nebo nemusí fungovat vůbec. | Hlavní |
| Změna konfigurace zařízení | Na zdrojovém zařízení byla zjištěna změna konfigurace. | Vedlejší |
| Přetečení vyrovnávací paměti souvislých událostí na stanici | Na zdrojovém zařízení byla zjištěna událost přetečení vyrovnávací paměti. Událost může způsobit poškození dat, zhroucení programu nebo spuštění škodlivého kódu. | Hlavní |
| Resetování kontroleru | Zdrojové zařízení odeslalo do cílového kontroleru příkaz pro obnovení. Kontroler se dočasně zastavil a automaticky se spustil. | Upozornění |
| Zastavení kontroleru | Zdrojové zařízení odeslalo do cílového kontroleru příkaz k zastavení. Kontroler přestane pracovat, dokud se neodešle spouštěcí příkaz. | Upozornění |
| Zařízení se nepovedlo přijmout dynamickou IP adresu. | Zdrojové zařízení je nakonfigurované tak, aby přijímalo dynamickou IP adresu ze serveru DHCP, ale nedostalo adresu. To indikuje chybu konfigurace v zařízení nebo provozní chybu na serveru DHCP. Doporučuje se informovat správce sítě o incidentu. | Hlavní |
| Zařízení má podezření na odpojení (neodpovídá). | Zdrojové zařízení nereagovalo na odeslaný příkaz. Je možné, že při odeslání příkazu byl odpojen. | Hlavní |
| Požadavek služby EtherNet/IP CIP se nezdařil. | Server vrátil kód chyby. To indikuje chybu serveru nebo neplatný požadavek klientem. | Hlavní |
| Příkaz protokolu zapouzdření sítě EtherNet/IP se nezdařil. | Server vrátil kód chyby. To indikuje chybu serveru nebo neplatný požadavek klientem. | Hlavní |
| Přetečení vyrovnávací paměti události v podstanici | Na zdrojovém zařízení byla zjištěna událost přetečení vyrovnávací paměti. Událost může způsobit poškození dat, zhroucení programu nebo spuštění škodlivého kódu. | Hlavní |
| Očekávaná operace zálohování neproběhla. | Mezi dvěma zařízeními se nevyskytla očekávaná aktivita zálohování nebo přenosu souborů. To může znamenat chyby v procesu zálohování nebo přenosu souborů. | Hlavní |
| Chyba příkazu GE SRTP | Server vrátil kód chyby. To indikuje chybu serveru nebo neplatný požadavek klientem. | Hlavní |
| Byl odeslán příkaz GE SRTP stop PLC. | Zdrojové zařízení odeslalo do cílového kontroleru příkaz k zastavení. Kontroler přestane pracovat, dokud se neodešle spouštěcí příkaz. | Upozornění |
| Řídicí blok Hus vyžaduje další konfiguraci. | Zdrojové zařízení odeslalo zprávu o tom, že zařízení potřebuje vystavení z provozu. To znamená, že řídicí blok Hus vyžaduje další konfiguraci a zprávy o úrovni Hus jsou částečně nebo zcela nefunkční. | Hlavní |
| Konfigurace sady dat hus se změnila. | Datová sada pro zprávu (identifikovaná ID protokolu) se změnila na zdrojovém zařízení. To znamená, že zařízení nahlásí pro tuto zprávu jinou datovou sadu. | Upozornění |
| Neočekávaný stav řadiče Honeywell | Řadič Honeywell odeslal neočekávanou diagnostickou zprávu upozorňující na změnu stavu. | Upozornění |
| Chyba klienta HTTP | Zdrojové zařízení iniciovalo neplatnou žádost. | Upozornění |
| Neplatná IP adresa | Systém zjistil provoz mezi zdrojovým zařízením a IP adresou, která je neplatnou adresou. To může znamenat špatnou konfiguraci nebo pokus o vygenerování neplatného provozu. | Vedlejší |
| Chyba ověřování Master-Slave | Proces ověřování mezi zdrojovým zařízením DNP3 (hlavním) a cílovým zařízením (z dokovací stanice) se nezdařil. | Vedlejší |
| Požadavek služby MMS se nezdařil. | Server vrátil kód chyby. To indikuje chybu serveru nebo neplatný požadavek klientem. | Hlavní |
| V rozhraní snímače se nezjistil žádný provoz. | Senzor zastavil detekci síťového provozu v síťovém rozhraní. | Kritické |
| Server OPC UA vyvolal událost, která vyžaduje pozornost uživatele. | Server OPC UA odeslal klientovi oznámení o události. Tento typ události vyžaduje pozornost uživatele. | Hlavní |
| Žádost o službu OPC UA se nezdařila. | Server vrátil kód chyby. To indikuje chybu serveru nebo neplatný požadavek klientem. | Hlavní |
| Odstanice se restartovala | Na zdrojovém zařízení byl zjištěn studený restart. To znamená, že zařízení bylo fyzicky vypnuto a znovu navráceno. | Upozornění |
| Často se restartují stanice | Na zdrojovém zařízení bylo zjištěno nadměrné množství studených restartů. To znamená, že zařízení se fyzicky vypnulo a znovu opakuje po nadměrném počtu. | Vedlejší |
| Konfigurace elektrárny se změnila. | Na zdrojovém zařízení byla zjištěna změna konfigurace. | Hlavní |
| Zjistila se poškozená konfigurace z nějakého stanoviště. | Toto zdrojové zařízení DNP3 (odstanice) oznámilo poškozenou konfiguraci. | Hlavní |
| Příkaz propracovaného DCP se nezdařil. | Server vrátil kód chyby. To indikuje chybu serveru nebo neplatný požadavek klientem. | Hlavní |
| Obnovení továrního nastavení zařízení | Zdrojové zařízení odeslalo příkaz pro obnovení továrního nastavení do cílového zařízení. Příkaz pro obnovení vymaže konfigurace propracovaného zařízení a zastaví jeho činnost. | Upozornění |
| Operace RPC se nezdařila | Server vrátil kód chyby. To indikuje chybu serveru nebo neplatný požadavek klientem. | Hlavní |
| Došlo ke změně konfigurace datové sady zpráv s ukázkovými hodnotami. | Datová sada pro zprávu (identifikovaná ID protokolu) se změnila na zdrojovém zařízení. To znamená, že zařízení nahlásí pro tuto zprávu jinou datovou sadu. | Upozornění |
| Neobnovitelné selhání podřízeného zařízení | Na zdrojovém zařízení se zjistila chyba neobnovitelné podmínky. Tento druh chyby obvykle indikuje selhání hardwaru nebo selhání při provádění určitého příkazu. | Hlavní |
| Podezření na problémy s hardwarem v trakčním stání | Na zdrojovém zařízení se zjistila chyba neobnovitelné podmínky. Tento druh chyby obvykle indikuje selhání hardwaru nebo selhání při provádění určitého příkazu. | Hlavní |
| Podezření na nereagující zařízení MODBUS | Zdrojové zařízení nereagovalo na odeslaný příkaz. Je možné, že při odeslání příkazu byl odpojen. | Vedlejší |
| Zjistil se provoz na rozhraní snímače. | Senzor obnovil detekci síťového provozu v síťovém rozhraní. | Upozornění |

## <a name="next-steps"></a>Další kroky

Můžete [spravovat události výstrah](how-to-manage-the-alert-event.md).
Zjistěte, jak [přeslat informace o výstrahách](how-to-forward-alert-information-to-partners.md).

---
title: Řešení potíží s průvodce – služba Azure Mobile Engagement.
description: Řešení potíží s příručky pro Azure Mobile Engagement
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 8b4275da-c0b4-4690-824a-48e9d7a1fc6e
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 64bd4849e3bb9a37fb32940548d5ecf01fffe0ca
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="troubleshooting-guide-for-service-issues"></a>Průvodci odstraňováním potíží problémů služby
> [!IMPORTANT]
> Na 3/31/2018 IT vyřadí Azure Mobile Engagement. Tato stránka bude odstraněna krátce po.
> 

Následují možných problémů se můžete setkat s jak spouští Azure Mobile Engagement.

## <a name="service-outages"></a>Výpadky
### <a name="issue"></a>Problém
* Problémy, které by mohly být způsobeno Azure Mobile Engagement výpadky.

### <a name="causes"></a>Způsobí, že
* Problémy, které by mohly být způsobeno Azure Mobile Engagement výpadky může být způsobeno několika různých problémy:
  * Izolované problémy, které původně zobrazit systémové ke všem Azure Mobile Engagement
  * Známé problémy, kvůli výpadku serveru (ne vždy zobrazuje stav serveru):
  * Plánování zpoždění, cílení na chyby, oznámení "BADGE" update problémy, statistiky zastavit shromažďování nabízené přestane pracovat, rozhraní API zastavení práce, nové aplikace nebo uživatele nelze vytvořit, chyb DNS a vypršení časového limitu v uživatelském rozhraní, rozhraní API nebo aplikace na zařízení.
  * Cloud závislostí výpadků [stav služby Azure](http://status.azure.com/)
  * Nabízená oznámení služby (PNS) závislostí výpadků
  * Výpadky obchodu s aplikacemi

1) Chcete-li otestujte, pokud je problém systémové můžete otestovat té samé funkce z jiné

* Integrovaná aplikace Azure Mobile Engagement
* Testovací zařízení
* Zkušební verze operačního systému zařízení
* Kampaň
* Uživatelský účet správce
* Prohlížeč (IE, Firefox, Chrome, atd.)
* Počítač

2) K testování, pokud se problém se týká pouze uživatelského rozhraní nebo rozhraní API:

* Stejnou funkci z rozhraní Azure Mobile Engagement i v Azure Mobile Engagement rozhraní API otestujte.

3) K testování, pokud se problém s vaší sítí mobilního telefonu:

* Test při připojení k Internetu prostřednictvím sítě Wi-Fi a při připojení prostřednictvím sítě 3G mobilního telefonu.
* Potvrďte, že brána firewall neblokuje Azure Mobile Engagement IP adresy nebo porty.

4) K testování, pokud se problém s vaším zařízením:

* Otestujte, pokud vaše zařízení je možné se připojit k Azure Mobile Engagement s jinou integrované aplikace Azure Mobile Engagement.
* Test, který může generovat události, úlohy a dojde k chybě z váš telefon, který je možné zobrazit v uživatelském rozhraní Azure Mobile Engagement. 
* Test při odeslání nabízených oznámení z uživatelského rozhraní Azure Mobile Engagement zařízení podle jeho ID zařízení. 

5) K testování, pokud se problém s vaší aplikací:

* Instalace a testování vaší aplikace z emulátoru místo z fyzického zařízení:

6) K testování, pokud se problém s upgrady operačního systému pro koncového uživatele zařízení, které vyžadují upgrade SDK řešení:

* Testování aplikace na různých zařízeních s různými verzemi operačního systému.
* Potvrďte, že používáte nejnovější verzi sady SDK.

## <a name="connectivity-and-incorrect-information-issues"></a>Připojení a nesprávné informace o problémech
### <a name="issue"></a>Problém
* Problémy s přihlášením se do rozhraní Azure Mobile Engagement.
* Chyby připojení se v Azure Mobile Engagement rozhraní API.
* Potíže s odesíláním značky informace o aplikaci prostřednictvím rozhraní API pro zařízení.
* Problémy stahování protokolů nebo exportovaná data z Azure Mobile Engagement.
* Nesprávné informace zobrazené v uživatelském rozhraní Azure Mobile Engagement.
* Nesprávné informace zobrazené v protokolech Azure Mobile Engagement.

### <a name="causes"></a>Způsobí, že
* Potvrďte, že váš uživatelský účet má dostatečná oprávnění k provedení úlohy.
* Potvrďte, že problém není izolovaná do jednoho počítače nebo v místní síti.
* Potvrďte, že služba Azure Mobile Engagement nemá hlášené výpadky.
* Přesvědčte se, vaše aplikace informace o označení souborů podívejte se na všech těchto pravidel:
  * Použití pouze UTF8 znakovou sadu (znakovou sadu ANSI není podporována.).
  * Použít čárku "," jako oddělovací znak (můžete otevřít službu požadavku k požadavku na změnu .csv oddělovací znak z čárkou "," na jiný znak, jako je například středníkem ";").
  * Používejte všechny malá písmena pro logické hodnoty "true" a "false".
  * Použijte soubor, který je menší než maximální velikost souboru 35 MB.


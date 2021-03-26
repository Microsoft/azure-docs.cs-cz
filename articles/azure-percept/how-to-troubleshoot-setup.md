---
title: Řešení potíží během prostředí pro instalaci Azure Percept DK
description: Získejte tipy pro řešení problémů s některými častými problémy nalezenými během prostředí pro instalaci.
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: template-how-to
ms.openlocfilehash: 7ce13cedff9afc25900c0bf75359ae49cc29fe19
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105608489"
---
# <a name="azure-percept-dk-setup-experience-troubleshooting-guide"></a>Průvodce odstraňováním potíží s prostředím pro instalaci Azure Percept DK

V následující tabulce najdete alternativní řešení běžných problémů zjištěných během [prostředí pro instalaci Azure PERCEPT DK](./quickstart-percept-dk-set-up.md). Pokud potíže přetrvávají, obraťte se na zákaznickou podporu Azure.

|Problém|Důvod|Alternativní řešení|
|:-----|:------|:----------|
|Při připojování ke stránkám pro registraci k účtu Azure nebo k Azure Portal se můžete automaticky přihlásit pomocí účtu uloženého v mezipaměti. Pokud se nejedná o účet, který byste chtěli použít, může to mít za následek prostředí, které není v dokumentaci konzistentní.|K tomu obvykle dochází z důvodu nastavení v prohlížeči na "zapamatování" účtu, který jste použili dříve.|Na stránce Azure klikněte na název vašeho účtu v pravém horním rohu a vyberte **Odhlásit** se. Pak se budete moct přihlásit pomocí správného účtu.|
|V seznamu dostupných Wi-Fi sítí se nezobrazuje přístupový bod Wi-Fi Azure Percept DK (SCZ-xxxx nebo APD-xxxx).|Obvykle se jedná o dočasný problém, který se vyřeší do 15 minut.|Počkejte, až se síť zobrazí. Pokud se nezobrazí za více než 15 minut, restartujte zařízení.|
|Připojení ke službě Azure Percept DK Wi-Fi přístupový bod se často odpojuje.|To může být způsobeno nedostatečným připojením mezi zařízením a hostitelským počítačem. Může to být také způsobeno rušením jiných Wi-Fi připojení v hostitelském počítači.|Ujistěte se, že antény jsou správně připojené ke vývojářské sadě. Pokud je Vývojářská sada daleko mimo hostitelský počítač, zkuste ji přesunout blíž. Vypněte všechna další připojení k Internetu, jako je LTE/5G, pokud jsou spuštěná v hostitelském počítači.|
|Hostitelský počítač zobrazí upozornění zabezpečení týkající se připojení k přístupovému bodu Azure Percept DK.|Jedná se o známý problém, který bude opraven v pozdější aktualizaci.|V průběhu instalace je bezpečné pokračovat v práci.|
|V seznamu síť se zobrazí přístup k Azure Percept DK Wi-Fi (SCZ-xxxx nebo APD-xxxx), ale připojení se nezdařila.|Důvodem může být dočasné poškození Wi-Fiho přístupového bodu sady pro vývojáře.|Restartujte sadu dev Kit a zkuste to znovu.|
|Během instalačního prostředí se nelze připojit k síti Wi-Fi.|Wi-Fi síť již musí mít připojení k Internetu, aby mohl komunikovat s Azure. Protokol EAP [PEAP/MSCHAP], neintegrované portály a připojení Enterprise EAP-TLS se momentálně nepodporuje.|Ujistěte se, že váš typ sítě Wi-Fi podporován a že má připojení k Internetu.|
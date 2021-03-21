---
title: Řešení potíží při zavádění prostředí Azure Percept DK
description: Získejte tipy pro řešení problémů s některými častými problémy zjištěnými během připojování.
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: d71cfa6ba52052e4b68175be84934c8b4294ed25
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "101662320"
---
# <a name="azure-percept-dk-onboarding-experience-troubleshooting-guide"></a>Průvodce odstraňováním potíží s registrací v Azure Percept DK

Tady jsou některé problémy, se kterými se můžete setkat během připojování k prostředí Azure Percept DK. Pokud po použití kroků v této příručce bude problém stále trvat. Kontaktujte zákaznickou podporu Azure.

|Problém|Důvod|Alternativní řešení|
|:-----|:------|:----------|
|Při připojování ke stránkám pro registraci k účtu Azure nebo k Azure Portal se můžete automaticky přihlásit pomocí účtu uloženého v mezipaměti. Pokud se nejedná o účet, který jste chtěli použít, může to mít za následek prostředí, které není v dokumentaci konzistentní.|K tomu obvykle dochází z důvodu nastavení v prohlížeči na "zapamatování" účtu, který jste použili dříve.|Na stránce Azure klikněte v pravém horním rohu stránky, kde se zobrazuje název účtu, a pak klikněte na Odhlásit se. Pak se budete moct přihlásit pomocí správného účtu.|
|Síť přístupového bodu Azure Percept DK (SCZ-xxxx) se nezobrazí v seznamu dostupných sítí Wi-Fi.|Obvykle se jedná o dočasný problém, který se s určitou dobou vyřeší trochu.|Počkejte, až se síť zobrazí. Pokud se nejedná o více než 15 minut, restartujte zařízení.|
|Připojení k přístupovému bodu Azure Percept DK se často odpojuje.|To může být způsobeno nedostatečným připojením mezi zařízením a hostitelským počítačem. Může to být také způsobeno rušením jiných Wi-Fi připojení v hostitelském počítači.|Ujistěte se, že antény jsou správně připojené ke vývojářské sadě. Pokud je Vývojářská sada daleko mimo hostitelský počítač, zkuste ji přesunout blíž. Vypněte všechna další připojení k Internetu, jako je LTE/5G, pokud jsou spuštěná v hostitelském počítači.|
|Hostitelský počítač zobrazí upozornění zabezpečení týkající se připojení k přístupovému bodu Azure Percept DK.|Jedná se o známý problém, který bude opraven v pozdější aktualizaci.|Přes DevKit Wi-Fi přístupového bodu je bezpečné postupovat prostřednictvím prostředí pro připojování.|
|Síť přístupového bodu Azure Percept DK (SCZ-xxxx) se zobrazí v seznamu síť, ale připojení se nezdařila.|Důvodem může být dočasné poškození DevKit Wi-Fi přístupového bodu.|Restartujte DevKit a zkuste to znovu.|
|Během instalačního prostředí se nelze připojit k síti Wi-Fi.|Síť Wi-Fi musí aktuálně mít připojení k Internetu, abychom mohli komunikovat s Azure. Protokol EAP [PEAP/MSCHAP], neintegrované portály a připojení Enterprise EAP-TLS se momentálně nepodporuje.|Ujistěte se, že typ Wi-Fi síť, ke které se připojujete, je podporovaný a má připojení k Internetu.|
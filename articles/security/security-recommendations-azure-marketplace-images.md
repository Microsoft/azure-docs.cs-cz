---
title: Doporučení zabezpečení pro Image Azure Marketplace | Dokumentace Microsoftu
description: Tento článek obsahuje doporučení pro Image obsažený v místě na trhu
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.date: 10/18/2017
ms.author: barclayn
ms.openlocfilehash: 9c02dc386852a32814669d38df6260822a5e4f99
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53308786"
---
# <a name="security-recommendations-for-azure-marketplace-images"></a>Doporučení zabezpečení pro Image Azure Marketplace

Doporučujeme vám, že každé řešení v souladu s následující doporučené konfigurace zabezpečení. To vám pomůže udržovat vysokou úroveň zabezpečení pro partnerské řešení imagí v Tržišti Azure Marketplace.

Tato doporučení může být také užitečné pro organizace, které nemají imagí v Tržišti Azure marketplace. Můžete chtít zkontrolujte vaše společnost Windows a Linuxem konfigurace image proti pokynů v následujících tabulkách.

## <a name="open-source-based-images"></a>Otevřít na základě zdroje obrázků

|||
|--------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Kategorie**                                                 | **Kontrola**                                                                                                                                                                                                                                                                              |
| Zabezpečení                                                     | Jsou nainstalované všechny nejnovější opravami zabezpečení pro Linuxové distribuce.                                                                                                                                                                                                              |
| Zabezpečení                                                     | Byla postupovali podle odvětví pokyny k zabezpečení pro konkrétní Linuxovou distribuci image virtuálního počítače.                                                                                                                                                                                     |
| Zabezpečení                                                     | Omezte tak zachovat minimální nároky na místo s pouze nezbytné role Windows serveru, funkcí, služeb a síťové porty pro možný útok.                                                                                                                                               |
| Zabezpečení                                                     | Kontrola zdrojového kódu a výsledné image virtuálního počítače malwaru.                                                                                                                                                                                                                                   |
| Zabezpečení                                                     | Image virtuálního pevného disku obsahuje pouze nezbytné uzamčené účty, které nemají výchozí hesla, která by umožnila interaktivního přihlášení; nezamýšlených.                                                                                                                                           |
| Zabezpečení                                                     | Pravidla brány firewall jsou zakázány, pokud aplikace funkčně spoléhá na informace, jako je například zařízení brány firewall.                                                                                                                                                                             |
| Zabezpečení                                                     | Veškeré citlivé informace byl odebrán z image virtuálního pevného disku, jako jsou klíče SSH testu, známé souboru hostitele, soubory protokolů a nepotřebné certifikáty.                                                                                                                                       |
| Zabezpečení                                                     | Doporučuje se, že by neměly používat LVM.                                                                                                                                                                                                                                            |
| Zabezpečení                                                     | Nejnovější verze požadované knihovny by měly být zahrnuty: </br> -OpenSSL verze 1.0 nebo vyšší </br> -Python 2.5 nebo novější (doporučujeme Python 2.6 +) </br> -Balíček Python pyasn1 Pokud ještě není nainstalovaný </br> -d.OpenSSL verze 1.0 nebo vyšší                                                                |
| Zabezpečení                                                     | Musí být zrušeno zaškrtnutí položky historie nebo prostředí bash                                                                                                                                                                                                                                             |
| Sítě                                                   | SSH server by měly být zahrnuty ve výchozím nastavení. Nastavte SSH keep alive do konfigurace sshd instrukci s tímto parametrem: ClientAliveInterval 180                                                                                                                                                        |
| Sítě                                                   | Bitová kopie by neměl obsahovat žádné vlastní síťová konfigurace. Odstraňte resolv.conf: `rm /etc/resolv.conf`                                                                                                                                                                                |
| Nasazení                                                   | By měl být nainstalován nejnovější Agent Azure Linux </br> – Musí být agent nainstalovaný za použití balíčku RPM nebo Deb.  </br> – Můžete také použít proces ruční instalace, ale jsou doporučené a upřednostňované balíčky Instalační služby. </br> – Pokud je ruční instalace agenta z úložiště GitHub, nejprve zkopírovat `waagent` do souboru `/usr/sbin` a spustit (jako uživatel root): </br>`# chmod 755 /usr/sbin/waagent` </br>`# /usr/sbin/waagent -install` </br>Konfigurační soubor agenta se umístí na `/etc/waagent.conf`.    |
| Nasazení                                                   | Zajišťuje, že podpora Azure může poskytnout partnerům pomocí konzoly sériového portu výstup, kdy je potřeba a poskytuje odpovídající časový limit pro připojení disku operačního systému z cloudového úložiště. Obrázek musí být přidán následující parametry na řádek pro spuštění jádra: `console=ttyS0 earlyprintk=ttyS0 rootdelay=300` |
| Nasazení                                                   | Žádné odkládací oddíl na disk s operačním systémem. Prohození mohou být požadována pro vytvoření na disku místního prostředku pomocí linuxového agenta.         |
| Nasazení                                                   | Doporučuje se, že se vytvoří jediný kořenový oddíl pro disk s operačním systémem.      |
| Nasazení                                                   | 64-bit pouze operační systém.                                                                                                                                                                                                                                                          |

## <a name="windows-server-based-images"></a>Bitové kopie na serveru systému Windows

|||
|-------------| -------------------------|
| **Kategorie**                                                     | **Kontrola**                                                                                                                                                                |
| Zabezpečení                                                         | Použijte zabezpečené základní image operačního systému. Virtuální pevný disk pro zdroj libovolné obrázku založená na Windows serveru musí být z imagí operačního systému Windows Server poskytuje prostřednictvím Microsoft Azure. |
| Zabezpečení                                                         | Nainstalujte všechny nejnovější aktualizace zabezpečení.                                                                                                                                     |
| Zabezpečení                                                         | Aplikace by neměl být závislý na s omezeným přístupem uživatelská jména, jako je například správce, kořenové a správce.                                                                |
| Zabezpečení                                                         | Na pevném disku operačního systému nepodporuje nástroj BitLocker Drive Encryption. BitLocker může být použita na datové disky.                                                            |
| Zabezpečení                                                         | Omezte tak zachovat minimální nároky na místo s pouze nezbytné role Windows serveru, funkce, služby a síťové porty, které jsou povolené pro možný útok.                         |
| Zabezpečení                                                         | Kontrola zdrojového kódu a výsledné image virtuálního počítače malwaru.                                                                                                                     |
| Zabezpečení                                                         | Nastavte aktualizace zabezpečení Image Windows serveru k automatické aktualizaci.                                                                                                                |
| Zabezpečení                                                         | Image virtuálního pevného disku obsahuje pouze nezbytné uzamčené účty, které nemají výchozí hesla, která by umožnila interaktivního přihlášení; nezamýšlených.                             |
| Zabezpečení                                                         | Pravidla brány firewall jsou zakázány, pokud aplikace funkčně spoléhá na informace, jako je například zařízení brány firewall.                                                               |
| Zabezpečení                                                         | Veškeré citlivé informace byl odebrán z image virtuálního pevného disku. Například byste měli odebrat soubor, soubory protokolu a nepotřebné certifikáty hostitele.                                              |
| Nasazení                                                       | 64-bit pouze operační systém.                            |

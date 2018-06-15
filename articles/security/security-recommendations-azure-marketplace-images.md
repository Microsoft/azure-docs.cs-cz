---
title: Doporučení zabezpečení pro Image Azure Marketplace | Microsoft Docs
description: Tento článek obsahuje doporučení pro bitové kopie, které jsou součástí Marketplace
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
ms.openlocfilehash: 4ae36f87c29975c82bb99f713893a9dc78a249e6
ms.sourcegitcommit: d6ad3203ecc54ab267f40649d3903584ac4db60b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2017
ms.locfileid: "23931132"
---
# <a name="security-recommendations-for-azure-marketplace-images"></a>Doporučení zabezpečení pro Azure Marketplace bitové kopie

Doporučujeme vám, že každé řešení v souladu s následující doporučené konfigurace zabezpečení. To vám pomůže udržují vysokou úroveň zabezpečení pro bitové kopie partnerských řešení v Azure Marketplace.

Tato doporučení může být také vhodné pro organizace, které nemají bitové kopie v Azure marketplace. Můžete zkontrolovat vaše společnost systém Windows a Linux konfigurace bitové kopie proti pokyny najít v následujících tabulkách.

## <a name="open-source-based-images"></a>Otevřete na základě zdrojové bitové kopie

|||
|--------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Kategorie**                                                 | **Kontrola**                                                                                                                                                                                                                                                                              |
| Zabezpečení                                                     | Jsou nainstalované všechny nejnovější opravy zabezpečení pro distribuci systému Linux.                                                                                                                                                                                                              |
| Zabezpečení                                                     | Byly provedeny odvětví pokyny k zabezpečení image virtuálního počítače pro konkrétní distribuci systému Linux.                                                                                                                                                                                     |
| Zabezpečení                                                     | Omezte prostor k útoku podle zachování minimální nároky s pouze potřebné role Windows serveru, funkce, služby a síťové porty.                                                                                                                                               |
| Zabezpečení                                                     | Skenování zdrojového kódu a výsledné image virtuálního počítače malwaru.                                                                                                                                                                                                                                   |
| Zabezpečení                                                     | Image virtuálního pevného disku obsahují jenom nezbytné uzamčení účtů, které nemají výchozí hesla, která umožňují interaktivní přihlášení; nezamýšlených.                                                                                                                                           |
| Zabezpečení                                                     | Pravidla brány firewall jsou zakázané, pokud aplikace funkčně spoléhá na, třeba zařízení brány firewall.                                                                                                                                                                             |
| Zabezpečení                                                     | Odebrali jsme všechny citlivé informace z image virtuálního pevného disku, například testovací klíče SSH, známé souboru hostitelů, soubory protokolů a zbytečné certifikáty.                                                                                                                                       |
| Zabezpečení                                                     | Doporučuje se, že by neměl používat LVM.                                                                                                                                                                                                                                            |
| Zabezpečení                                                     | Nejnovější verze požadované knihovny by měly být zahrnuty: </br> -OpenSSL verze 1.0 nebo novější </br> -Python 2.5 nebo novější (Python 2.6 + se důrazně doporučuje) </br> -Balíček pyasn1 Python, pokud ještě není nainstalován </br> -d.OpenSSL v 1.0 nebo vyšší                                                                |
| Zabezpečení                                                     | Záznamy historie bash/prostředí je potřeba smazat.                                                                                                                                                                                                                                             |
| Sítě                                                   | Ve výchozím nastavení se zahrnuje serverem SSH. Nastavte zachování připojení SSH zachovat na sshd konfigurace následujících možností: ClientAliveInterval 180                                                                                                                                                        |
| Sítě                                                   | Bitové kopie nesmí obsahovat žádné vlastní síť konfigurace. Odstraňte resolv.conf:`rm /etc/resolv.conf`                                                                                                                                                                                |
| Nasazení                                                   | Nejnovější Azure Linux Agent by měly být nainstalovány. </br> – Agenta musí být nainstalován pomocí ot. / min nebo bázi Deb balíčku.  </br> – Můžete také používat proces ruční instalaci, ale jsou doporučené a upřednostňované balíčky Instalační služby systému. </br> – Pokud ruční instalace agenta z úložiště githubu, nejdříve zkopírovat `waagent` do souboru `/usr/sbin` a spusťte (jako uživatel root): </br>`# chmod 755 /usr/sbin/waagent` </br>`# /usr/sbin/waagent -install` </br>Konfigurační soubor agenta se umístí na `/etc/waagent.conf`.    |
| Nasazení                                                   | Zajišťuje, aby poskytoval podporu Azure našimi partnery pomocí konzoly sériového portu výstup v případě potřeby a zadejte adekvátní časový limit pro připojení disku operačního systému z cloudového úložiště. Bitová kopie musí přidána následující parametry na řádek spouštěcí jádra:`console=ttyS0 earlyprintk=ttyS0 rootdelay=300` |
| Nasazení                                                   | Žádné přepnutí oddílu na disku operačního systému. Swap může požadovat pro vytvoření na místní prostředek disku agenta systému Linux.         |
| Nasazení                                                   | Doporučujeme, vytvořený oddíl jeden kořenový pro disk operačního systému.      |
| Nasazení                                                   | 64bitová verze jenom operační systém.                                                                                                                                                                                                                                                          |

## <a name="windows-server-based-images"></a>Bitových kopií systému Windows na serveru

|||
|-------------| -------------------------|
| **Kategorie**                                                     | **Kontrola**                                                                                                                                                                |
| Zabezpečení                                                         | Použijte zabezpečení základní bitovou kopii operačního systému. Virtuální pevný disk používá pro zdroj všechny bitové kopie založené na Windows serveru musí být z bitové kopie operačního systému Windows Server, poskytované prostřednictvím Microsoft Azure. |
| Zabezpečení                                                         | Nainstalujte všechny nejnovější aktualizace zabezpečení.                                                                                                                                     |
| Zabezpečení                                                         | Aplikace by neměl mít závislost na názvy uživatele s omezeným přístupem, například správce, kořenové a správce.                                                                |
| Zabezpečení                                                         | Nástroj BitLocker Drive Encryption není podporována na pevném disku operačního systému. BitLocker se může použít na datové disky.                                                            |
| Zabezpečení                                                         | Omezte prostor k útoku podle zachování minimální nároky s pouze potřebné role Windows serveru, funkce, služby a síťové porty, které jsou povolené.                         |
| Zabezpečení                                                         | Skenování zdrojového kódu a výsledné image virtuálního počítače malwaru.                                                                                                                     |
| Zabezpečení                                                         | Nastavení aktualizací zabezpečení bitové kopie systému Windows Server pro automatickou aktualizaci.                                                                                                                |
| Zabezpečení                                                         | Image virtuálního pevného disku obsahují jenom nezbytné uzamčení účtů, které nemají výchozí hesla, která umožňují interaktivní přihlášení; nezamýšlených.                             |
| Zabezpečení                                                         | Pravidla brány firewall jsou zakázané, pokud aplikace funkčně spoléhá na, třeba zařízení brány firewall.                                                               |
| Zabezpečení                                                         | Odebrali jsme všechny citlivé informace z image virtuálního pevného disku. Například byste měli odebrat soubor HOSTITELŮ, soubory protokolů a zbytečné certifikáty.                                              |
| Nasazení                                                       | 64bitová verze jenom operační systém.                            |

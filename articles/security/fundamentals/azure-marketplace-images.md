---
title: Doporučení zabezpečení pro Azure Marketplace image | Microsoft Docs
description: Tento článek obsahuje doporučení pro Image zahrnuté na trhu.
services: security
documentationcenter: na
author: barclayn
manager: barbkess
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.date: 01/11/2019
ms.author: barclayn
ms.openlocfilehash: 3d6b1ed2a3800058acba9fe5df2137811afed74a
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300788"
---
# <a name="security-recommendations-for-azure-marketplace-images"></a>Doporučení zabezpečení pro Azure Marketplace image

Doporučujeme, aby každé řešení odpovídalo následujícím doporučením konfigurace zabezpečení. To pomáhá udržovat vysokou úroveň zabezpečení pro obrázky partnerských řešení v Azure Marketplace.

Tato doporučení můžou být užitečná i pro organizace, které nemají image na Azure Marketplace. Možná budete chtít ověřit konfigurace bitových kopií pro Windows a Linux vaší společnosti podle pokynů uvedených v následujících tabulkách:

## <a name="open-source-based-images"></a>Otevřené image založené na zdroji

|||
|--------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Kategorie**                                                 | **Zda**                                                                                                                                                                                                                                                                              |
| Zabezpečení                                                     | Nainstalují se všechny nejnovější opravy zabezpečení pro distribuci systému Linux.                                                                                                                                                                                                              |
| Zabezpečení                                                     | Následovala se pravidla pro zabezpečení image virtuálního počítače pro konkrétní distribuci systému Linux.                                                                                                                                                                                     |
| Zabezpečení                                                     | Omezte prostor pro útoky tím, že podržíte minimální nároky jenom na nezbytné role, funkce, služby a síťové porty Windows serveru.                                                                                                                                               |
| Zabezpečení                                                     | Naskenujte zdrojový kód a výslednou image virtuálního počítače pro malware.                                                                                                                                                                                                                                   |
| Zabezpečení                                                     | Image VHD obsahuje jenom nezbytné uzamčené účty, které nemají výchozí hesla, která by umožňovala interaktivní přihlášení. žádné zadní dveře.                                                                                                                                           |
| Zabezpečení                                                     | Pravidla brány firewall jsou zakázaná, pokud je na nich funkce, jako je třeba zařízení brány firewall, nespoléhá na ně.                                                                                                                                                                             |
| Zabezpečení                                                     | Z image virtuálního pevného disku se odebraly všechny citlivé informace, jako je například test klíčů SSH, známý soubor hostitelů, soubory protokolů a nepotřebné certifikáty.                                                                                                                                       |
| Zabezpečení                                                     | Doporučuje se, aby se LVM nepoužívala.                                                                                                                                                                                                                                            |
| Zabezpečení                                                     | Měli byste zahrnout nejnovější verze požadovaných knihoven: </br> -OpenSSL verze 1.0 nebo vyšší </br> -Python 2,5 nebo novější (důrazně se doporučuje Python 2.6 +) </br> – Balíček python pyasn1, pokud ještě není nainstalovaný </br> -d. OpenSSL verze v 1,0 nebo vyšší                                                                |
| Zabezpečení                                                     | Musí být vymazány položky historie bash/shell.                                                                                                                                                                                                                                             |
| Sítě                                                   | Ve výchozím nastavení by měl být zahrnut Server SSH. Nastavte možnost SSH Keep on sshd config pomocí následující možnosti: ClientAliveInterval 180                                                                                                                                                        |
| Sítě                                                   | Image by neměla obsahovat žádnou vlastní konfiguraci sítě. Odstraňte soubor resolv. conf:`rm /etc/resolv.conf`                                                                                                                                                                                |
| Nasazení                                                   | Měl by se nainstalovat nejnovější agent Azure Linux. </br> -Agent by měl být nainstalovaný pomocí balíčku ot./min. nebo Deb.  </br> – Můžete použít také proces ruční instalace, ale doporučuje se balíčky Instalační služby a jsou preferované. </br> – Pokud chcete agenta nainstalovat ručně z úložiště GitHubu, napřed ho `waagent` zkopírujte do `/usr/sbin` a spusťte (jako kořen): </br>`# chmod 755 /usr/sbin/waagent` </br>`# /usr/sbin/waagent -install` </br>Konfigurační soubor agenta je umístěn na adrese `/etc/waagent.conf`.    |
| Nasazení                                                   | Zajistí, že podpora Azure může poskytnout našim partnerům výstup sériové konzoly, pokud je to potřeba, a zajistit adekvátní časový limit pro připojení disku s operačním systémem z cloudového úložiště. Image musí do spouštěcího řádku jádra přidat následující parametry:`console=ttyS0 earlyprintk=ttyS0 rootdelay=300` |
| Nasazení                                                   | Na disku s operačním systémem není žádný odkládací oddíl. Na disku místního prostředku se dá požádat Agent pro Linux k vytvoření swapu.         |
| Nasazení                                                   | Doporučuje se vytvořit jeden kořenový oddíl pro disk s operačním systémem.      |
| Nasazení                                                   | 64 pouze bitový operační systém.                                                                                                                                                                                                                                                          |

## <a name="windows-server-based-images"></a>Image založené na Windows serveru

|||
|-------------| -------------------------|
| **Kategorie**                                                     | **Zda**                                                                                                                                                                |
| Zabezpečení                                                         | Použijte zabezpečenou základní bitovou kopii operačního systému. Virtuální pevný disk, který se používá pro zdroj všech imagí založených na Windows serveru, musí být z imagí operačního systému Windows Server, které poskytuje Microsoft Azure. |
| Zabezpečení                                                         | Nainstalujte všechny nejnovější aktualizace zabezpečení.                                                                                                                                     |
| Zabezpečení                                                         | Aplikace by neměly mít závislost na omezených uživatelských jménech, jako je správce, kořen a správce.                                                                |
| Zabezpečení                                                         | Nástroj BitLocker Drive Encryption je povolená pro pevné disky s operačním systémem i pro datové pevné disky.                                                             |
| Zabezpečení                                                         | Omezte prostor pro útoky tím, že podržíte minimální nároky jenom na potřebné role, funkce, služby a síťové porty Windows serveru.                         |
| Zabezpečení                                                         | Naskenujte zdrojový kód a výslednou image virtuálního počítače pro malware.                                                                                                                     |
| Zabezpečení                                                         | Nastavte aktualizaci zabezpečení imagí Windows serveru na automatickou aktualizaci.                                                                                                                |
| Zabezpečení                                                         | Image VHD obsahuje jenom nezbytné uzamčené účty, které nemají výchozí hesla, která by umožňovala interaktivní přihlášení. žádné zadní dveře.                             |
| Zabezpečení                                                         | Pravidla brány firewall jsou zakázaná, pokud je na nich funkce, jako je třeba zařízení brány firewall, nespoléhá na ně.                                                               |
| Zabezpečení                                                         | Z image virtuálního pevného disku se odebraly všechny citlivé informace. Například je třeba odebrat soubor hostitelů, soubory protokolu a nadbytečné certifikáty.                                              |
| Nasazení                                                       | 64 pouze bitový operační systém.                            |

---
title: Doporučení zabezpečení pro Azure Marketplace image | Microsoft Docs
description: Tento článek obsahuje doporučení pro Image zahrnuté na trhu.
services: security
documentationcenter: na
author: terrylanfear
manager: barbkess
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.date: 01/11/2019
ms.author: terrylan
ms.openlocfilehash: 7c317a0b4fea0c981b227bace00c1b8924fd582c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89536378"
---
# <a name="security-recommendations-for-azure-marketplace-images"></a>Doporučení zabezpečení pro Azure Marketplace image

Bitová kopie musí splňovat tato doporučení pro konfiguraci zabezpečení. To pomáhá udržovat vysokou úroveň zabezpečení pro obrázky partnerských řešení v Azure Marketplace.

Před odesláním na Image vždycky spusťte detekci ohrožení zabezpečení. Pokud zjistíte ohrožení zabezpečení ve vaší vlastní publikované imagi, je nutné včas informovat zákazníky o ohrožení zabezpečení a o tom, jak je opravit.

## <a name="open-source-based-images"></a>Otevřené image založené na zdroji

| Kategorie | Zaškrtnout |
| -------- | ----- |
| Zabezpečení                                                     | Nainstalujte všechny nejnovější opravy zabezpečení pro distribuci systému Linux.                                                                                                                                                                                                              |
| Zabezpečení                                                     | Postupujte podle pokynů pro odvětví a zabezpečte image virtuálního počítače pro konkrétní distribuci systému Linux.                                                                                                                                                                                     |
| Zabezpečení                                                     | Omezte prostor pro útoky tím, že podržíte minimální nároky jenom na nezbytné role, funkce, služby a síťové porty Windows serveru.                                                                                                                                               |
| Zabezpečení                                                     | Naskenujte zdrojový kód a výslednou image virtuálního počítače pro malware.                                                                                                                                                                                                                                   |
| Zabezpečení                                                     | Image VHD obsahuje jenom nezbytné uzamčené účty, které nemají výchozí hesla, které by umožňovaly interaktivní přihlášení. žádné zadní dveře.                                                                                                                                           |
| Zabezpečení                                                     | Zakáže pravidla brány firewall, pokud je aplikace funkčně nespoléhá, jako je třeba zařízení brány firewall.                                                                                                                                                                             |
| Zabezpečení                                                     | Odeberte všechny citlivé informace z image VHD, jako je například test klíčů SSH, známého souboru hostitelů, souborů protokolu a zbytečných certifikátů.                                                                                                                                       |
| Zabezpečení                                                     | Vyhněte se použití LVM.                                                                                                                                                                                                                                            |
| Zabezpečení                                                     | Zahrnout nejnovější verze požadovaných knihoven: </br> -OpenSSL verze 1.0 nebo vyšší </br> -Python 2,5 nebo novější (důrazně se doporučuje Python 2.6 +) </br> – Balíček python pyasn1, pokud ještě není nainstalovaný </br> -d. OpenSSL verze v 1,0 nebo vyšší                                                                |
| Zabezpečení                                                     | Vymazat položky historie bash/shell.                                                                                                                                                                                                                                             |
| Sítě                                                   | Ve výchozím nastavení zahrňte Server SSH. Nastavte možnost SSH Keep on sshd config pomocí následující možnosti: ClientAliveInterval 180.                                                                                                                                                        |
| Sítě                                                   | Odeberte z image libovolnou vlastní konfiguraci sítě. Odstraňte soubor resolv. conf: `rm /etc/resolv.conf` .                                                                                                                                                                                |
| Nasazení                                                   | Nainstalujte nejnovějšího agenta Azure Linux.</br> – Nainstalujte pomocí balíčku ot./min. nebo Deb.  </br> – Můžete použít také proces ruční instalace, ale doporučuje se balíčky Instalační služby a jsou preferované. </br> – Pokud chcete agenta nainstalovat ručně z úložiště GitHubu, napřed ho zkopírujte `waagent` do `/usr/sbin` a spusťte (jako kořen): </br>`# chmod 755 /usr/sbin/waagent` </br>`# /usr/sbin/waagent -install` </br>Konfigurační soubor agenta je umístěn na adrese `/etc/waagent.conf` . |
| Nasazení                                                   | Pokud je to potřeba, zajistěte, aby podpora Azure poskytovala našim partnerům výstup sériové konzoly a poskytovala přiměřený časový limit pro připojení disku s operačním systémem z cloudového Přidejte následující parametry do spouštěcího řádku jádra Image: `console=ttyS0 earlyprintk=ttyS0 rootdelay=300` . |
| Nasazení                                                   | Na disku s operačním systémem není žádný odkládací oddíl. Na disku místního prostředku se dá požádat Agent pro Linux k vytvoření swapu.         |
| Nasazení                                                   | Vytvořte jeden kořenový oddíl pro disk s operačním systémem.      |
| Nasazení                                                   | 64 pouze bitový operační systém.                                                                                                                                                                                                                                                          |

## <a name="windows-server-based-images"></a>Image založené na Windows serveru

| Kategorie | Zaškrtnout |
|--------- | ----- |
| Zabezpečení                                                         | Použijte zabezpečenou základní bitovou kopii operačního systému. Virtuální pevný disk, který se používá pro zdroj všech imagí založených na Windows serveru, musí být z imagí operačního systému Windows Server, které poskytuje Microsoft Azure. |
| Zabezpečení                                                         | Nainstalujte všechny nejnovější aktualizace zabezpečení.                                                                                                                                     |
| Zabezpečení                                                         | Aplikace by neměly záviset na jménech uživatelů s omezeným oprávněním, jako je správce, kořen nebo správce.                                                                |
| Zabezpečení                                                         | Povolte nástroj BitLocker Drive Encryption pro pevné disky operačního systému i pro datové pevné disky.                                                             |
| Zabezpečení                                                         | Omezte prostor pro útoky tím, že podržíte minimální nároky jenom na potřebné role, funkce, služby a síťové porty Windows serveru.                         |
| Zabezpečení                                                         | Naskenujte zdrojový kód a výslednou image virtuálního počítače pro malware.                                                                                                                     |
| Zabezpečení                                                         | Nastavte aktualizaci zabezpečení imagí Windows serveru na automatickou aktualizaci.                                                                                                                |
| Zabezpečení                                                         | Image VHD obsahuje jenom nezbytné uzamčené účty, které nemají výchozí hesla, které by umožňovaly interaktivní přihlášení. žádné zadní dveře.                             |
| Zabezpečení                                                         | Zakáže pravidla brány firewall, pokud je aplikace funkčně nespoléhá, jako je třeba zařízení brány firewall.                                                               |
| Zabezpečení                                                         | Odeberte všechny citlivé informace z image VHD, včetně souborů hostitelů, souborů protokolu a zbytečných certifikátů.                                              |
| Nasazení                                                       | 64 pouze bitový operační systém.                            |

I když vaše organizace nemá image na webu Azure Marketplace, zvažte možnost zkontrolovat konfigurace imagí pro Windows a Linux na těchto doporučeních.


---
title: Doporučení zabezpečení pro image Azure Marketplace | Dokumenty společnosti Microsoft
description: Tento článek obsahuje doporučení pro obrázky obsažené na trhu
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
ms.openlocfilehash: 3925e39824d1702ff43a6b981ac997ddab658b96
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548659"
---
# <a name="security-recommendations-for-azure-marketplace-images"></a>Doporučení zabezpečení pro image Azure Marketplace

Obrázek musí splňovat tato doporučení konfigurace zabezpečení. To pomáhá udržovat vysokou úroveň zabezpečení pro image partnerských řešení na Azure Marketplace.

Před odesláním bitové kopie vždy spouštějte zjišťování ohrožení zabezpečení. Pokud zjistíte chybu zabezpečení ve vlastní publikované bitové kopii, musíte zákazníky včas informovat o této chybě zabezpečení i o tom, jak ji opravit.

## <a name="open-source-based-images"></a>Obrázky založené na otevřených zdrojích

|||
|--------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Kategorie**                                                 | **Zkontrolujte**                                                                                                                                                                                                                                                                              |
| Zabezpečení                                                     | Nainstalujte všechny nejnovější opravy zabezpečení pro distribuci Linuxu.                                                                                                                                                                                                              |
| Zabezpečení                                                     | Postupujte podle oborových pokynů k zabezpečení image virtuálního počítače pro konkrétní distribuci Linuxu.                                                                                                                                                                                     |
| Zabezpečení                                                     | Omezte prostor pro útok tím, že zachováte minimální nároky na to, aby byly role, funkce, služby a síťové porty systému Windows Server.                                                                                                                                               |
| Zabezpečení                                                     | Naskenujte zdrojový kód a výslednou image virtuálního nástroje na malware.                                                                                                                                                                                                                                   |
| Zabezpečení                                                     | Obrázek virtuálního pevného disku obsahuje pouze nezbytné uzamčené účty, které nemají výchozí hesla, která by umožňovala interaktivní přihlášení; žádné zadní dveře.                                                                                                                                           |
| Zabezpečení                                                     | Zakažte pravidla brány firewall, pokud se na ně aplikace funkčně nespoléhá, například zařízení brány firewall.                                                                                                                                                                             |
| Zabezpečení                                                     | Odeberte všechny citlivé informace z bitové kopie virtuálního pevného disku, jako jsou testovací klíče SSH, známý soubor hostitelů, soubory protokolu a nepotřebné certifikáty.                                                                                                                                       |
| Zabezpečení                                                     | Nepoužívejte LVM.                                                                                                                                                                                                                                            |
| Zabezpečení                                                     | Zahrňte nejnovější verze požadovaných knihoven: </br> - OpenSSL v1.0 nebo vyšší </br> - Python 2.5 nebo vyšší (Python 2.6+ je vysoce doporučeno) </br> - Python pyasn1 balíček, pokud již není nainstalován </br> - d.OpenSSL v 1.0 nebo vyšší                                                                |
| Zabezpečení                                                     | Zrušte zaškrtnutí položek historie Bash/Shell.                                                                                                                                                                                                                                             |
| Sítě                                                   | Ve výchozím nastavení zahrňte server SSH. Nastavte SSH keep alive na sshd config s následující možností: ClientAliveInterval 180.                                                                                                                                                        |
| Sítě                                                   | Odeberte z bitové kopie libovolnou vlastní konfiguraci sítě. Odstraňte resolv.conf: `rm /etc/resolv.conf`.                                                                                                                                                                                |
| Nasazení                                                   | Nainstalujte nejnovějšího agenta Azure Linuxu.</br> - Nainstalujte pomocí balíčku RPM nebo Deb.  </br> - Můžete také použít ruční instalaci, ale instalační balíčky jsou doporučené a preferované. </br> - Pokud nainstalujete agenta ručně z úložiště `waagent` GitHub, nejprve zkopírujte soubor `/usr/sbin` a spusťte (jako root): </br>`# chmod 755 /usr/sbin/waagent` </br>`# /usr/sbin/waagent -install` </br>Konfigurační soubor `/etc/waagent.conf`agenta je umístěn na adrese . |
| Nasazení                                                   | Ujistěte se, že podpora Azure může v případě potřeby poskytovat našim partnerům výstup sériové konzoly a poskytovat dostatečný časový limit pro připojení disku operačního systému z cloudového úložiště. Do zaváděcí linky jádra obrázku přidejte následující parametry: `console=ttyS0 earlyprintk=ttyS0 rootdelay=300`. |
| Nasazení                                                   | Na disku s osa není žádný odkládací oddíl. Odkládací lze požádat o vytvoření na disku místního prostředku agentem Linuxu.         |
| Nasazení                                                   | Vytvořte jeden kořenový oddíl pro disk operačního systému.      |
| Nasazení                                                   | Pouze 64bitový operační systém.                                                                                                                                                                                                                                                          |

## <a name="windows-server-based-images"></a>Bitové kopie založené na systému Windows Server

|||
|-------------| -------------------------|
| **Kategorie**                                                     | **Zkontrolujte**                                                                                                                                                                |
| Zabezpečení                                                         | Použijte zabezpečenou základní bitovou kopii operačního systému. Virtuální pevný disk používaný pro zdroj libovolné bitové kopie založené na systému Windows Server musí pocházet z bitových kopií operačního systému Windows Server poskytovaných prostřednictvím Microsoft Azure. |
| Zabezpečení                                                         | Nainstalujte všechny nejnovější aktualizace zabezpečení.                                                                                                                                     |
| Zabezpečení                                                         | Aplikace by neměly záviset na omezených uživatelských jménech, jako je správce, kořen nebo správce.                                                                |
| Zabezpečení                                                         | Povolte šifrování jednotky BitLocker pro pevné disky operačního samu i datové pevné disky.                                                             |
| Zabezpečení                                                         | Omezte prostor pro útok tím, že zachováte minimální nároky na minimální nároky, pokud jsou povoleny pouze role, funkce, služby a síťové porty systému Windows Server.                         |
| Zabezpečení                                                         | Naskenujte zdrojový kód a výslednou image virtuálního nástroje na malware.                                                                                                                     |
| Zabezpečení                                                         | Nastavte aktualizaci zabezpečení bitových kopií systému Windows Server na automatickou aktualizaci.                                                                                                                |
| Zabezpečení                                                         | Obrázek virtuálního pevného disku obsahuje pouze nezbytné uzamčené účty, které nemají výchozí hesla, která by umožňovala interaktivní přihlášení; žádné zadní dveře.                             |
| Zabezpečení                                                         | Zakažte pravidla brány firewall, pokud se na ně aplikace funkčně nespoléhá, například zařízení brány firewall.                                                               |
| Zabezpečení                                                         | Odstraňte všechny citlivé informace z bitové kopie virtuálního pevného disku, včetně souborů HOSTS, souborů protokolu a nepotřebných certifikátů.                                              |
| Nasazení                                                       | Pouze 64bitový operační systém.                            |

I v případě, že vaše organizace nemá image na webu Azure Marketplace, zvažte kontrolu konfigurace bitových kopií Windows a Linuxu proti těmto doporučením.

## <a name="contacting-customers"></a>Kontaktování zákazníků

Identifikace zákazníků a jejich kontaktních e-mailů:

1.  Na portálu partnerů cloudu na levém kolejnici vyberte **Insights**.
2.  Na kartě **Objednávky a použití** použijte pole **Počáteční datum** a Koncové **datum** k dotazování na využití v rámci požadovaného období. To ukazuje, které předplatná Azure byly použity pro nabídku na denní bázi. Exportovat tato data. 
3.  Podobně na kartě **Zákazník** se dotazujte a exportujte zákaznickou základnu.
4.  Spárovat ID předplatného od kroku 2 k ID předplatného z kroku 3 najít potřebné informace o zákaznících.

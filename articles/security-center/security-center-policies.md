---
title: Nastavení zásad zabezpečení ve službě Azure Security Center | Dokumentace Microsoftu
description: Tento článek vám pomůže s konfigurací zásad zabezpečení ve službě Azure Security Center.
services: security-center
documentationcenter: na
author: terrylan
manager: mbaldwin
editor: ''
ms.assetid: 3b9e1c15-3cdb-4820-b678-157e455ceeba
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2018
ms.author: yurid
ms.openlocfilehash: fed4a587d2258cdab35d09f5e3947a8cacade157
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34364382"
---
# <a name="set-security-policies-in-azure-security-center"></a>Nastavení zásad zabezpečení ve službě Azure Security Center
Tento článek vám pomůže s konfigurací zásad zabezpečení ve službě Security Center.

## <a name="how-security-policies-work"></a>Způsob fungování zásad zabezpečení
Security Center automaticky vytváří výchozí zásady zabezpečení pro každé z vašich předplatných Azure. Ve službě Security Center můžete zásady upravovat a monitorovat jejich dodržování.

> [!NOTE]
> Zásady služby Security Center teď můžete rozšířit pomocí služby Azure Policy, která je ve verzi Limited Preview. Pokud se chcete připojit k verzi Preview, přejděte na web [pro registraci do Azure Policy](https://aka.ms/getpolicy). Další informace najdete v tématu [Integrace zásad zabezpečení ve službě Security Center se službou Azure Policy](security-center-azure-policy.md).

Požadavky na zabezpečení pro prostředky používané pro vývoj nebo testování se můžou lišit od požadavků pro prostředky, které se používají v produkčních aplikacích. Aplikace pracující s regulovanými daty, jako jsou identifikovatelné osobní údaje, můžou vyžadovat vyšší úroveň zabezpečení. Zásady zabezpečení povolené ve službě Azure Security Center mají vliv na doporučení zabezpečení a monitorování a pomohou vám najít potenciální nedostatky zabezpečení a zmírnit hrozby. Další informace o určení, která možnost je pro vás vhodná, najdete v tématu [Průvodce plánováním a provozem služby Azure Security Center](security-center-planning-and-operations-guide.md).

## <a name="edit-security-policies"></a>Upravení zásad zabezpečení
Ve službě Security Center můžete upravit výchozí zásady zabezpečení pro každé z vašich předplatných Azure. Pokud chcete upravit nějakou zásadu zabezpečení, musíte být vlastníkem, přispěvatelem nebo správcem zabezpečení daného předplatného. Pokud chcete konfigurovat zásady zabezpečení ve službě Security Center, postupujte takto:

1. Přihlaste se k portálu Azure.

2. Na řídicím panelu **Security Center** v části **Obecné** vyberte **Zásady zabezpečení**.

3. Vyberte předplatné, pro které chcete povolit zásady zabezpečení.

4. V části **Součásti zásad** vyberte **Zásady zabezpečení**.  
    Toto jsou výchozí zásady přiřazené službou Security Center. Můžete zapnout nebo vypnout dostupná doporučení zabezpečení.

5. Až budete s úpravami hotovi, klikněte na **Uložit**.

## <a name="available-security-policy-definitions"></a>Dostupné definice zásad zabezpečení

Následující tabulku můžete použít jako referenci pro pochopení definic zásad, které jsou dostupné ve výchozích zásadách zabezpečení:

| Zásada | Co zásada dělá |
| --- | --- |
| Aktualizace systému |Načte denní seznam dostupných aktualizací zabezpečení a důležitých aktualizací z webu Windows Update nebo ze služby Windows Server Update Services. Načtený seznam závisí na službě nakonfigurované pro vaše virtuální počítače a doporučuje instalaci chybějících aktualizací. V systémech Linux zásada pro určení balíčků, pro které jsou dostupné aktualizace, využívá systém správy balíčků, který je součástí dané distribuce. Také kontroluje aktualizace zabezpečení a důležité aktualizace z virtuálních počítačů služby [Azure Cloud Services](../cloud-services/cloud-services-how-to-configure-portal.md). |
| Konfigurace zabezpečení |Denně analyzuje konfigurace operačního systému k určení problémů, které by mohly způsobit zranitelnost virtuálních počítačů vůči útoku. Zásada také doporučuje změny v konfiguraci pro odstranění těchto ohrožení zabezpečení. Další informace o tom, jaké konkrétní konfigurace se monitorují, najdete v [seznamu doporučených standardních hodnot](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). (V tomto okamžiku není Windows Server 2016 plně podporovaný.) |
| Ochrana koncových bodů |Doporučuje nastavení ochrany koncových bodů pro všechny virtuální počítače s Windows, aby bylo možné identifikovat a odstraňovat viry, spyware a další škodlivý software. |
| Šifrování disku |Doporučuje povolit šifrování disku ve všech virtuálních počítačích pro zvýšení ochrany dat při nečinnosti. |
| Skupiny zabezpečení sítě |Doporučuje konfiguraci [skupin zabezpečení sítě](../virtual-network/security-overview.md), které řídí příchozí a odchozí přenosy dat do virtuálních počítačů s veřejnými koncovými body. Pokud neurčíte jinak, skupiny zabezpečení sítě nakonfigurované pro určitou podsíť se dědí do všech síťových rozhraní virtuálních počítačů. Kromě kontroly toho, jestli je skupina zabezpečení sítě nakonfigurovaná, tato zásada také vyhodnocuje pravidla zabezpečení příchozích dat a zjišťuje pravidla, která povolují příchozí přenosy dat. |
| Brána firewall webových aplikací |Doporučuje nastavení brány firewall webových aplikací na virtuálních počítačích, pokud je splněna jedna z následujících podmínek: <ul><li>Používá se [veřejná IP adresa na úrovni instance](../virtual-network/virtual-networks-instance-level-public-ip.md) a pravidla zabezpečení příchozích dat pro přidruženou skupinu zabezpečení sítě jsou nakonfigurovaná tak, aby povolovala přístup k portu 80 a 443.</li><li>Používá se IP adresa s vyrovnáváním zatížení a přidružené vyrovnávání zatížení i pravidla překladu adres příchozích dat jsou nakonfigurované tak, aby povolovaly přístup k portu 80 a 443. Další informace najdete v tématu [Podpora služby Load Balancer v Azure Resource Manageru](../load-balancer/load-balancer-arm.md).</li> |
| Brána firewall příští generace |Rozšiřuje ochranu sítě nad rámec skupin zabezpečení sítě, které jsou integrované v Azure. Security Center zjišťuje nasazení, pro která se doporučuje brána firewall nové generace, a potom můžete nastavit virtuální zařízení. |
| Detekce hrozeb a auditování SQL |Doporučuje povolit auditování přístupu k vaší databázi SQL za účelem šetření dodržování předpisů a rozšířeného zjišťování hrozeb. |
| Šifrování SQL |Doporučuje povolit šifrování v klidovém stavu pro vaši databázi SQL, přidružené zálohy a soubory transakčních protokolů. I v případě, že dojde k porušení zabezpečení vašich dat, nebudou čitelná. |
| Posouzení ohrožení zabezpečení |Doporučuje, abyste na vašem virtuálním počítači nainstalovali řešení posouzení ohrožení zabezpečení. |
| Šifrování úložiště |Tato funkce je aktuálně dostupná pro objekty blob a Soubory Azure. Pokud povolíte šifrování služby Storage, budou se šifrovat jenom nová data a veškeré stávající soubory v účtu úložiště zůstanou nezašifrované. |
| Síťový přístup JIT |Pokud je síťový přístup JIT (právě včas) povolený, Security Center uzamkne příchozí provoz do vašich virtuálních počítačů Azure vytvořením pravidla skupiny zabezpečení sítě. Na virtuálním počítači vyberete porty, na které má být příchozí provoz uzamčen. Další informace najdete v tématu popisujícím [správu přístupu k virtuálním počítačům pomocí metody právě včas](https://docs.microsoft.com/azure/security-center/security-center-just-in-time). |


## <a name="next-steps"></a>Další kroky
V tomto článku jste zjistili, jak ve službě Security Center konfigurovat zásady zabezpečení. Další informace o službě Security Center najdete v následujících článcích:

* [Průvodce plánováním a provozem služby Azure Security Center](security-center-planning-and-operations-guide.md) – Zjistěte, jak naplánovat a pochopit aspekty návrhu pro Azure Security Center.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se sledovat stav svých prostředků Azure.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Sledování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md) – Zjistěte, jak pomocí Azure Security Center sledovat stav vašich partnerských řešení.
* [Azure Security Center – nejčastější dotazy](security-center-faq.md) – Získejte odpovědi na nejčastější dotazy týkající se použití této služby.
* [Blog o zabezpečení Azure](http://blogs.msdn.com/b/azuresecurity/): Přečtěte si příspěvky o zabezpečení a dodržování předpisů Azure.

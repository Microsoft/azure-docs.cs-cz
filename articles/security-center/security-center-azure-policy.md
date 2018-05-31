---
title: Integrace zásad zabezpečení ve službě Azure Security Center se službou Azure Policy | Dokumentace Microsoftu
description: Tento dokument vám pomůže s konfigurací integrace zásad zabezpečení ve službě Azure Security Center se službou Azure Policy.
services: security-center
documentationcenter: na
author: terrylan
manager: mbaldwin
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2018
ms.author: yurid
ms.openlocfilehash: aec29ac1ccf9386615e7603898f071fe9cda44cf
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34364331"
---
# <a name="integrate-security-center-security-policies-with-azure-policy"></a>Integrace zásad zabezpečení ve službě Security Center se službou Azure Policy
Tento článek vám pomůže s konfigurací zásad zabezpečení ve službě Azure Security Center, které využívají službu Azure Policy.

## <a name="how-security-policies-work"></a>Způsob fungování zásad zabezpečení
Security Center automaticky vytváří výchozí zásady zabezpečení pro každé z vašich předplatných Azure. Zásady můžete upravit ve službě Security Center nebo můžete [Azure Policy](http://docs.microsoft.com/azure/azure-policy/azure-policy-introduction) využít k následujícím akcím:
* Vytvoření nových definic zásad
* Přiřazení zásad napříč skupinami pro správu, které mohou reprezentovat celou organizaci nebo organizační jednotku v rámci této organizace
* Monitorování dodržování zásad

> [!NOTE]
> Zásady Azure jsou ve verzi Limited Preview. Pokud se chcete zapojit, přejděte na web [pro registraci do Azure Policy](https://aka.ms/getpolicy). Další informace o službě Azure Policy najdete v tématu [Vytvoření a správa zásad pro vynucování dodržování předpisů](http://docs.microsoft.com/azure/azure-policy/create-manage-policy).

## <a name="edit-security-policies"></a>Upravení zásad zabezpečení
Ve službě Security Center můžete upravit výchozí zásady zabezpečení pro každé z vašich předplatných Azure. Pokud chcete upravit zásady zabezpečení, musíte být vlastníkem, přispěvatelem nebo správcem zabezpečení daného předplatného nebo skupiny pro správu, která ho obsahuje. Pokud chcete zobrazit zásady zabezpečení ve službě Security Center, postupujte takto:

1. Přihlaste se k portálu Azure.

2. Na řídicím panelu **Security Center** v části **Obecné** vyberte **Zásady zabezpečení**.

    ![Okno Správa zásad](./media/security-center-azure-policy/security-center-policies-fig10.png)

3. Vyberte předplatné, pro které chcete povolit zásady zabezpečení.  

4. V části **Součásti zásad** vyberte **Zásady zabezpečení**.  
    Otevře se okno **Základy**.

    ![Součásti zásad](./media/security-center-azure-policy/security-center-policies-fig12.png)

5. Pokud chcete odstranit definici zásad, v části **Policies and Parameters** (Zásady a parametry) vedle definice, kterou chcete odstranit, vyberte **Odstranit**.

6. Klikněte na **Uložit**.  
    Otevře se okno **Available Definitions** (Dostupné definice), které zobrazuje výchozí zásady přiřazené službě Security Center prostřednictvím Azure Policy.

7. (Volitelné) V okně **Available Definitions** (Dostupné definice) proveďte jednu z následujících akcí:

    * Pokud chcete přidat definici zásady, vyberte znaménko plus (+) vedle definice.

    ![Dostupné definice zásad](./media/security-center-azure-policy/security-center-policies-fig11.png)

    * Pokud chcete zobrazit podrobné vysvětlení zásady, vyberte ji.  
    Otevře se okno s **náhledem** definice. Zobrazuje popis definice a odkaz na kód JSON, který poskytuje strukturu [definice zásady](../azure-policy/policy-definition.md).

    ![Okno s náhledem definice](./media/security-center-azure-policy/security-center-policies-fig14.png)

7. Až budete s úpravami hotovi, klikněte na **Uložit**.

## <a name="available-security-policy-definitions"></a>Dostupné definice zásad zabezpečení

Následující tabulku můžete použít jako referenci pro pochopení definic zásad, které jsou dostupné ve výchozích zásadách zabezpečení:

| Zásada | Co tato povolená zásada dělá |
| --- | --- |
| Aktualizace systému |Načte denní seznam dostupných aktualizací zabezpečení a důležitých aktualizací z webu Windows Update nebo ze služby Windows Server Update Services. Načtený seznam závisí na službě nakonfigurované pro vaše virtuální počítače a doporučuje instalaci chybějících aktualizací. V systémech Linux zásada pro určení balíčků, pro které jsou dostupné aktualizace, využívá systém správy balíčků, který je součástí dané distribuce. Také kontroluje aktualizace zabezpečení a důležité aktualizace z virtuálních počítačů služby [Azure Cloud Services](../cloud-services/cloud-services-how-to-configure-portal.md). |
| Konfigurace zabezpečení |Denně analyzuje konfigurace operačního systému k určení problémů, které by mohly způsobit zranitelnost virtuálních počítačů vůči útoku. Zásada také doporučuje změny v konfiguraci pro odstranění těchto ohrožení zabezpečení. Další informace o tom, jaké konkrétní konfigurace se monitorují, najdete v [seznamu doporučených standardních hodnot](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). (V tomto okamžiku není Windows Server 2016 plně podporovaný.) |
| Ochrana koncových bodů |Doporučuje nastavení ochrany koncových bodů pro všechny virtuální počítače s Windows, aby bylo možné identifikovat a odstraňovat viry, spyware a další škodlivý software. |
| Šifrování disku |Doporučuje povolit šifrování disku ve všech virtuálních počítačích pro zvýšení ochrany dat při nečinnosti. |
| Skupiny zabezpečení sítě |Doporučuje konfiguraci [skupin zabezpečení sítě](../virtual-network/security-overview.md), které řídí příchozí a odchozí přenosy dat do virtuálních počítačů s veřejnými koncovými body. Pokud neurčíte jinak, skupiny zabezpečení sítě nakonfigurované pro určitou podsíť se dědí do všech síťových rozhraní virtuálních počítačů. Kromě kontroly toho, jestli je skupina zabezpečení sítě nakonfigurovaná, tato zásada také vyhodnocuje pravidla zabezpečení příchozích dat a zjišťuje pravidla, která povolují příchozí přenosy dat. |
| Brána firewall webových aplikací |Doporučuje nastavení brány firewall webových aplikací na virtuálních počítačích, pokud je splněna jedna z následujících podmínek: <ul><li>Používá se [veřejná IP adresa na úrovni instance](../virtual-network/virtual-networks-instance-level-public-ip.md) a pravidla zabezpečení příchozích dat pro přidruženou skupinu zabezpečení sítě jsou nakonfigurovaná tak, aby povolovala přístup k portu 80 a 443.</li><li>Používá se IP adresa s vyrovnáváním zatížení a přidružené vyrovnávání zatížení i pravidla překladu adres příchozích dat jsou nakonfigurované tak, aby povolovaly přístup k portu 80 a 443. Další informace najdete v tématu [Podpora služby Load Balancer v Azure Resource Manageru](../load-balancer/load-balancer-arm.md).</li> |
| Brána firewall příští generace |Rozšiřuje ochranu sítě nad rámec skupin zabezpečení sítě, které jsou integrované v Azure. Security Center zjišťuje nasazení, pro která se doporučuje brána firewall nové generace, a potom můžete nastavit virtuální zařízení. |
| Detekce hrozeb a auditování SQL |Doporučuje, abyste povolili auditování přístupu ke službě Azure Database za účelem šetření dodržování předpisů a rozšířeného zjišťování hrozeb. |
| Šifrování SQL |Doporučuje povolit šifrování v klidovém stavu pro služby Azure SQL Database, přidružené zálohy a soubory protokolů transakcí. I v případě, že dojde k porušení zabezpečení vašich dat, nebudou čitelná. |
| Posouzení ohrožení zabezpečení |Doporučuje, abyste na vašem virtuálním počítači nainstalovali řešení posouzení ohrožení zabezpečení. |
| Šifrování úložiště |Tato funkce je aktuálně dostupná pro Soubory Azure a Azure Blob storage. Pokud povolíte šifrování služby Storage, budou se šifrovat jenom nová data a veškeré stávající soubory v účtu úložiště zůstanou nezašifrované. |
| Síťový přístup JIT |Pokud je síťový přístup JIT (právě včas) povolený, Security Center uzamkne příchozí provoz do vašich virtuálních počítačů Azure vytvořením pravidla skupiny zabezpečení sítě. Na virtuálním počítači vyberete porty, na které má být příchozí provoz uzamčen. Další informace najdete v tématu popisujícím [správu přístupu k virtuálním počítačům pomocí metody právě včas](https://docs.microsoft.com/azure/security-center/security-center-just-in-time). |


## <a name="next-steps"></a>Další kroky
V tomto článku jste zjistili, jak ve službě Security Center konfigurovat zásady zabezpečení. Další informace o službě Security Center najdete v následujících článcích:

* [Průvodce plánováním a provozem služby Azure Security Center](security-center-planning-and-operations-guide.md) – Zjistěte, jak naplánovat a pochopit aspekty návrhu pro Azure Security Center.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se sledovat stav svých prostředků Azure.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Sledování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md) – Zjistěte, jak pomocí Azure Security Center sledovat stav vašich partnerských řešení.
* [Azure Security Center – nejčastější dotazy](security-center-faq.md) – Získejte odpovědi na nejčastější dotazy týkající se použití této služby.
* [Blog o zabezpečení Azure](http://blogs.msdn.com/b/azuresecurity/): Přečtěte si příspěvky o zabezpečení a dodržování předpisů Azure.

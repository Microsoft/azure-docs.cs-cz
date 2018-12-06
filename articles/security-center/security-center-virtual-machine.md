---
title: Azure Security Center a Azure Virtual Machines | Dokumentace Microsoftu
description: Tento dokument vám pomůže porozumět tomu, jak Azure Security Center může zabezpečit službu Azure Virtual Machines.
services: security-center
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: 5fe5a12c-5d25-430c-9d47-df9438b1d7c5
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/24/2017
ms.author: yurid
ms.openlocfilehash: 394897976fc7453d0639d985806d7226cc059a6c
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52956478"
---
# <a name="azure-security-center-and-azure-virtual-machines"></a>Azure Security Center a Azure Virtual Machines
[Azure Security Center](https://azure.microsoft.com/services/security-center/) pomáhá předcházet hrozbám, zjišťovat je a reagovat na ně. Poskytuje integrované bezpečnostní sledování a správu zásad ve vašich předplatných Azure, pomáhá zjišťovat hrozby, kterých byste si jinak nevšimli, a spolupracuje s řadou řešení zabezpečení.

Tento článek ukazuje, jak vám Security Center může pomoci zabezpečit službu Azure Virtual Machines.

## <a name="why-use-security-center"></a>Proč používat Security Center?
Security Center vám pomůže chránit data virtuálních počítačů v Azure tím, že poskytuje vhled do nastavení zabezpečení vašich virtuálních počítačů. Když Security Center chrání vaše virtuální počítače, jsou k dispozici následující možnosti:

* Nastavení zabezpečení operačního systému s doporučenými konfiguračními pravidly
* Zabezpečení systému a chybějící kritické aktualizace
* Doporučení ochrany koncových bodů
* Ověření šifrování disku
* Posouzení a náprava ohrožení zabezpečení
* Detekce hrozeb

Security Center kromě toho, že pomáhá chránit vaše virtuální počítače Azure, také poskytuje monitorování a správu zabezpečení pro služby Cloud Services, App Services, Virtual Networks a další. 

> [!NOTE]
> Další informace o Azure Security Center najdete v tématu [Seznámení s Azure Security Center](security-center-intro.md).
> 
> 

## <a name="prerequisites"></a>Požadavky
Abyste mohli začít s Azure Security Center, je třeba znát a zvážit následující skutečnosti:

* Musíte mít předplatné Microsoft Azure. V tématu [Ceny Security Center](https://azure.microsoft.com/pricing/details/security-center/) najdete další informace o úrovních Free a Standard služby Security Center.
* Naplánujte své přijetí Security Center. V tématu [Průvodce plánováním a provozem Azure Security Center](security-center-planning-and-operations-guide.md) najdete další informace o tom, na co si dát pozor při plánování a provozu.
* Informace týkající se podpory operačních systémů najdete v tématu [Nejčastější dotazy k Azure Security Center](security-center-faq.md). 

## <a name="set-security-policy"></a>Nastavení zásad zabezpečení
Je nutné povolit shromažďování dat, aby Azure Security Center mohl shromažďovat informace, které potřebuje k poskytování doporučení a výstrah generovaných na základě vámi nakonfigurovaných zásad zabezpečení. Na obrázku níže vidíte, že **Shromažďování dat** bylo **Zapnuto**.

Zásady zabezpečení definují sadu ovládacích prvků doporučenou pro prostředky v rámci daného předplatného nebo skupiny prostředků. Před povolením zásad zabezpečení je nutné mít povolené shromažďování dat. Security Center shromažďuje data z vašich virtuálních počítačů za účelem posouzení jejich stavu, poskytování doporučení zabezpečení a upozorňování na hrozby. V Security Center určíte zásady pro vaše předplatná Azure nebo skupiny prostředků na základě toho, jaké má vaše společnost požadavky na zabezpečení, a podle typu aplikací nebo citlivosti dat v jednotlivých předplatných. 

![Zásady zabezpečení](./media/security-center-virtual-machine/security-center-virtual-machine-fig1.png)

> [!NOTE]
> Další informace o jednotlivých dostupných **Zásadách prevence** najdete v článku [Nastavení zásad zabezpečení](security-center-azure-policy.md).
> 
> 

## <a name="manage-security-recommendations"></a>Správa doporučení zabezpečení
Security Center analyzuje stav zabezpečení vašich prostředků Azure. Když Security Center identifikuje potenciální ohrožení zabezpečení, vytvoří doporučení. Doporučení vás provedou procesem konfigurace potřebných kontrol.

Po nastavení zásad zabezpečení bude Security Center analyzovat stav zabezpečení vašich prostředků Azure za účelem identifikace potenciálních ohrožení zabezpečení. Doporučení jsou zobrazena ve formátu tabulky, kde každý řádek představuje jedno konkrétní doporučení. Níže uvedená tabulka obsahuje některé příklady doporučení pro virtuální počítače Azure a popis toho, co jednotlivá doporučení dělají, když je použijete. Když vyberete doporučení, zobrazí se informace, které vám ukážou, jak dané doporučení implementovat v Security Center.

| Doporučení | Popis |
| --- | --- |
| [Povolení shromažďování dat pro předplatná](security-center-enable-data-collection.md) |Doporučuje, abyste zapnuli shromažďování dat v zásadách zabezpečení pro každé ze svých předplatných a všechny virtuální počítače ve svých předplatných. |
| [Náprava ohrožení zabezpečení operačního systému](security-center-remediate-os-vulnerabilities.md) |Doporučuje upravit konfiguraci operačního systému tak, aby byla v souladu s doporučenými pravidly konfigurace, například abyste zakázali ukládání hesel. |
| [Instalace aktualizací systému](security-center-apply-system-updates.md) |Doporučuje nasazení chybějících aktualizací zabezpečení systému a kritických aktualizací do virtuálních počítačů. |
| [Restartování po aktualizacích systému](security-center-apply-system-updates.md#reboot-after-system-updates) |Doporučuje, abyste restartovali virtuální počítač k dokončení procesu instalace aktualizací systému. |
| [Instalace Endpoint Protection](security-center-install-endpoint-protection.md) |Doporučuje, abyste do virtuálních počítačů nainstalovali antimalwarové programy (platí pouze pro virtuální počítače s Windows). |
| [Povolení agenta virtuálního počítače](security-center-enable-vm-agent.md) |Umožňuje vám zobrazit, které virtuální počítače vyžadují agenta virtuálního počítače. Agent virtuálního počítače musí být nainstalovaný na virtuálních počítačích, aby mohl poskytovat vyhledávání oprav, vyhledávání směrných plánů a antimalwarové programy. Agent virtuálního počítače je ve výchozím nastavení nainstalován na virtuálních počítačích nasazených z Azure Marketplace. V článku [Agenti a rozšíření virtuálních počítačů – Část 2](https://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) najdete informace o tom, jak agenta virtuálního počítače nainstalovat. |
| [Použití šifrování disku](security-center-apply-disk-encryption.md) |Doporučuje, abyste disky svých virtuálních počítačů zašifrovali pomocí služby Azure Disk Encryption (platí pro virtuální počítače s Windows a Linuxem). Na virtuálním počítači se doporučuje šifrování svazku operačního systému i svazku s daty. |
| [Není nainstalováno posouzení ohrožení zabezpečení](security-center-vulnerability-assessment-recommendations.md) |Doporučuje, abyste na vašem virtuálním počítači nainstalovali řešení posouzení ohrožení zabezpečení. |
| [Náprava ohrožení zabezpečení](security-center-vulnerability-assessment-recommendations.md#review-the-recommendation) |Umožňuje vám zobrazit ohrožení zabezpečení systému a aplikací zjištěná řešením posouzení ohrožení zabezpečení nainstalovaným na vašem virtuálním počítači. |

> [!NOTE]
> Další informace o doporučeních najdete v článku [Správa doporučení zabezpečení](security-center-recommendations.md).
> 
> 

## <a name="monitor-security-health"></a>Monitorování stavu zabezpečení
Po povolení [zásad zabezpečení](security-center-azure-policy.md) pro prostředky předplatného bude služba Security Center analyzovat zabezpečení vašich prostředků, aby identifikovala potenciální ohrožení zabezpečení.  V okně **Stav zabezpečení prostředků** můžete zobrazit stav zabezpečení svých prostředků spolu s případnými problémy. Když na dlaždici **Stav zabezpečení prostředků** kliknete na **Virtuální počítače**, otevře se okno **Virtuální počítače**, které obsahuje doporučení pro vaše virtuální počítače. 

![Stav zabezpečení](./media/security-center-virtual-machine/security-center-virtual-machine-fig2.png)

## <a name="manage-and-respond-to-security-alerts"></a>Správa a reakce na výstrahy zabezpečení
Security Center automaticky shromažďuje, analyzuje a integruje data protokolu z vašich prostředků Azure, sítě a připojených partnerských řešení (jako jsou brány firewall a řešení ochrany koncových bodů), aby se zjistily skutečné hrozby a snížil počet falešných poplachů. Díky využívání různorodých [možností detekce](security-center-detection-capabilities.md) je služba Security Center schopna generovat výstrahy zabezpečení seřazené podle priority a tím vám pomáhá rychle prozkoumat problém a poskytuje doporučení k napravení možných útoků.

![Výstrahy zabezpečení](./media/security-center-virtual-machine/security-center-virtual-machine-fig3.png)

Vyberte výstrahu zabezpečení, abyste se dozvěděli další informace o událostech, které výstrahu vygenerovaly a kroky, pokud existují, které je třeba provést k nápravě útoku. Výstrahy zabezpečení jsou seskupené podle [typu](security-center-alerts-type.md) a data.

## <a name="see-also"></a>Další informace najdete v tématech
Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení v Azure Security Center](security-center-azure-policy.md) – Zjistěte, jak konfigurovat zásady zabezpečení pro svá předplatná Azure a skupiny prostředků.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Nejčastější dotazy k Azure Security Center](security-center-faq.md) – Přečtěte si nejčastější dotazy k používání této služby.


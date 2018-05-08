---
title: Azure Security Center a virtuální počítače Azure s Linuxem | Dokumentace Microsoftu
description: Tento dokument vám pomůže porozumět tomu, jak Azure Security Center může zabezpečit službu Azure Virtual Machines.
services: security-center
documentationcenter: na
author: terrylan
manager: swadhwa
editor: ''
ms.assetid: 5fe5a12c-5d25-430c-9d47-df9438b1d7c5
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/03/2017
ms.author: yurid
ms.openlocfilehash: e08444cbfdb095c79d164de06dda078c6a242b3f
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="azure-security-center-and-azure-virtual-machines-with-linux"></a>Azure Security Center a virtuální počítače s Linuxem
[Azure Security Center](https://azure.microsoft.com/services/security-center/) pomáhá předcházet hrozbám, zjišťovat je a reagovat na ně. Poskytuje integrované bezpečnostní sledování a správu zásad ve vašich předplatných Azure, pomáhá zjišťovat hrozby, kterých byste si jinak nevšimli, a spolupracuje s řadou řešení zabezpečení.

Tento článek ukazuje, jak vám Security Center může pomoci zabezpečit virtuální počítače Azure s operačním systémem Linux.

## <a name="why-use-security-center"></a>Proč používat Security Center?
Security Center pomáhá chránit data virtuálních počítačů v Azure tím, že poskytuje vhled do nastavení zabezpečení vašich virtuálních počítačů a monitoruje hrozby. Security Center může u virtuálních počítačů monitorovat: 

* Nastavení zabezpečení operačního systému s doporučenými konfiguračními pravidly
* Zabezpečení systému a chybějící kritické aktualizace
* Doporučení ochrany koncových bodů
* Ověření šifrování disku
* Síťové útoky (k dispozici pouze ve [standardní verzi](https://azure.microsoft.com/pricing/details/security-center/))

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

![Zásady zabezpečení](./media/security-center-linux-virtual-machine/security-center-linux-virtual-machine-fig1.png)

> [!NOTE]
> Další informace o jednotlivých dostupných **Zásadách prevence** najdete v článku [Nastavení zásad zabezpečení](security-center-policies.md).
> 

## <a name="manage-security-recommendations"></a>Správa doporučení zabezpečení
Security Center analyzuje stav zabezpečení vašich prostředků Azure. Když Security Center identifikuje potenciální ohrožení zabezpečení, vytvoří doporučení. Doporučení vás provedou procesem konfigurace potřebných kontrol.

Po nastavení zásad zabezpečení bude Security Center analyzovat stav zabezpečení vašich prostředků Azure za účelem identifikace potenciálních ohrožení zabezpečení. Doporučení jsou zobrazena ve formátu tabulky, kde každý řádek představuje jedno konkrétní doporučení. Následující tabulka obsahuje některé příklady doporučení pro virtuální počítače Azure s operačním systémem Linux a popis toho, co jednotlivá doporučení dělají, když je použijete. Když vyberete doporučení, zobrazí se informace, které vám ukážou, jak dané doporučení implementovat v Security Center.

| Doporučení | Popis |
| --- | --- |
| [Povolení shromažďování dat pro předplatná](security-center-enable-data-collection.md) |Doporučuje, abyste zapnuli shromažďování dat v zásadách zabezpečení pro každé ze svých předplatných a všechny virtuální počítače ve svých předplatných. |
| [Náprava ohrožení zabezpečení operačního systému](security-center-remediate-os-vulnerabilities.md) |Doporučuje upravit konfiguraci operačního systému tak, aby byla v souladu s doporučenými pravidly konfigurace, například abyste zakázali ukládání hesel. |
| [Instalace aktualizací systému](security-center-apply-system-updates.md) |Doporučuje nasazení chybějících aktualizací zabezpečení systému a kritických aktualizací do virtuálních počítačů. |
| [Restartování po aktualizacích systému](security-center-apply-system-updates.md#reboot-after-system-updates) |Doporučuje, abyste restartovali virtuální počítač k dokončení procesu instalace aktualizací systému. |
| [Povolení agenta virtuálního počítače](security-center-enable-vm-agent.md) |Umožňuje vám zobrazit, které virtuální počítače vyžadují agenta virtuálního počítače. Agent virtuálního počítače musí být nainstalovaný na virtuálních počítačích, aby mohl poskytovat vyhledávání oprav, vyhledávání směrných plánů a antimalwarové programy. Agent virtuálního počítače je ve výchozím nastavení nainstalován na virtuálních počítačích nasazených z Azure Marketplace. V článku [Agenti a rozšíření virtuálních počítačů – Část 2](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) najdete informace o tom, jak agenta virtuálního počítače nainstalovat. |
| [Použití šifrování disku](security-center-apply-disk-encryption.md) |Doporučuje, abyste disky svých virtuálních počítačů zašifrovali pomocí služby Azure Disk Encryption (platí pro virtuální počítače s Windows a Linuxem). Na virtuálním počítači se doporučuje šifrování svazku operačního systému i svazku s daty. |


> [!NOTE]
> Další informace o doporučeních najdete v článku [Správa doporučení zabezpečení](security-center-recommendations.md).
> 

## <a name="monitor-security-health"></a>Monitorování stavu zabezpečení
Po povolení [zásad zabezpečení](security-center-policies.md) pro prostředky předplatného bude služba Security Center analyzovat zabezpečení vašich prostředků, aby identifikovala potenciální ohrožení zabezpečení.  V okně **Stav zabezpečení prostředků** můžete zobrazit stav zabezpečení svých prostředků spolu s případnými problémy. Když na dlaždici **Stav zabezpečení prostředků** kliknete na **Virtuální počítače**, otevře se okno **Virtuální počítače**, které obsahuje doporučení pro vaše virtuální počítače. 

![Stav zabezpečení](./media/security-center-virtual-machine/security-center-virtual-machine-fig2.png)

## <a name="manage-and-respond-to-security-alerts"></a>Správa a reakce na výstrahy zabezpečení
Security Center automaticky shromažďuje, analyzuje a integruje data protokolu z vašich prostředků Azure, sítě a připojených partnerských řešení (jako jsou brány firewall a řešení ochrany koncových bodů), aby se zjistily skutečné hrozby a snížil počet falešných poplachů. Díky využívání různorodých [možností detekce](security-center-detection-capabilities.md) je služba Security Center schopna generovat výstrahy zabezpečení seřazené podle priority a tím vám pomáhá rychle prozkoumat problém a poskytuje doporučení k napravení možných útoků.

![Výstrahy zabezpečení](./media/security-center-virtual-machine/security-center-virtual-machine-fig3.png)

Vyberte výstrahu zabezpečení, abyste se dozvěděli další informace o událostech, které výstrahu vygenerovaly a kroky, pokud existují, které je třeba provést k nápravě útoku. Výstrahy zabezpečení jsou seskupené podle [typu](security-center-alerts-type.md) a data.

## <a name="monitor-security-health"></a>Monitorování stavu zabezpečení
Po povolení [zásad zabezpečení](security-center-policies.md) pro prostředky předplatného bude služba Security Center analyzovat zabezpečení vašich prostředků, aby identifikovala potenciální ohrožení zabezpečení.  V okně **Stav zabezpečení prostředků** můžete zobrazit stav zabezpečení svých prostředků spolu s případnými problémy. Když na dlaždici **Stav zabezpečení prostředků** kliknete na **Virtuální počítače**, otevře se okno **Virtuální počítače**, které obsahuje doporučení pro vaše virtuální počítače. 

![Stav zabezpečení](./media/security-center-linux-virtual-machine/security-center-linux-virtual-machine-fig4.png)

Pokud kliknete na toto doporučení, zobrazí se další podrobnosti o konkrétních akcích, které by se měly provést pro vyřešení těchto problémů. Podrobnosti se zobrazí v dolní části okna v části **Doporučení**. 

![Stav zabezpečení 2](./media/security-center-linux-virtual-machine/security-center-linux-virtual-machine-fig5.png)


## <a name="see-also"></a>Viz také
Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení v Azure Security Center](security-center-policies.md) – Zjistěte, jak konfigurovat zásady zabezpečení pro svá předplatná Azure a skupiny prostředků.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Nejčastější dotazy k Azure Security Center](security-center-faq.md) – Přečtěte si nejčastější dotazy k používání této služby.


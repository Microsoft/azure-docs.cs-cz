---
title: Zvýšení zabezpečení pomocí doporučení Azure Security Center | Dokumenty společnosti Microsoft
description: " Zjistěte, jak pomocí zásad zabezpečení a doporučení v Centru zabezpečení Azure zmírnit útok zabezpečení. "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2019
ms.author: memildin
ms.openlocfilehash: a1034eb47010da2b0e795ee8c79646f06151cac1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77603283"
---
# <a name="use-azure-security-center-recommendations-to-enhance-security"></a>Použití doporučení služby Azure Security Center k vylepšení zabezpečení
Pravděpodobnost významné události zabezpečení můžete snížit konfigurací zásad zabezpečení a implementací doporučení poskytovaných službou Azure Security Center. Tento článek ukazuje, jak pomocí zásad zabezpečení a doporučení v Centru zabezpečení pomoci zmírnit útok zabezpečení. 

Security Center automaticky spouští nepřetržité prohledávače a analyzuje stav zabezpečení vašich prostředků Azure. Pokud Security Center identifikuje potenciální slabá místa zabezpečení, vytvoří doporučení, která vás provedou procesem konfigurace potřebných ovládacích prvků zabezpečení. Security Center aktualizuje svá doporučení do 24 hodin, s následujícími výjimkami:

- Doporučení konfigurace zabezpečení operačního systému jsou aktualizována do 48 hodin
- Doporučení týkající se ochrany koncových bodů jsou aktualizována do 8 hodin

## <a name="scenario"></a>Scénář
Tento scénář ukazuje, jak pomocí Centra zabezpečení snížit pravděpodobnost incidentu zabezpečení sledováním doporučení Centra zabezpečení a přijetím opatření. Scénář používá fiktivní společnost Contoso a role uvedené v [průvodci plánováním a operacemi](security-center-planning-and-operations-guide.md#security-roles-and-access-controls)Centra zabezpečení . V tomto scénáři se zaměřujeme na role následujících osob:

![Role scénáře](./media/security-center-using-recommendations/scenario-roles.png)

Contoso nedávno migrovala některé místní prostředky do Azure. Společnost Contoso chce chránit své prostředky a snížit zranitelnost svých prostředků v cloudu.

## <a name="use-azure-security-center"></a>Používejte Azure Security Center.
David, z it zabezpečení společnosti Contoso, se již rozhodl nastoupit do Centra zabezpečení v předplatných společnosti Contoso do Centra zabezpečení Azure, aby zabránil a zjistil slabá místa zabezpečení. 

Security Center automaticky analyzuje stav zabezpečení prostředků Azure společnosti Contoso a použije výchozí zásady zabezpečení. Pokud Security Center identifikuje potenciální slabá místa zabezpečení, vytvoří **doporučení** na základě ovládacích prvků nastavených v zásadách zabezpečení. 

David spouští standardní úroveň Azure Security ve všech svých předplatných, aby získal úplnou sadu doporučení a funkcí zabezpečení, které jsou k dispozici. Jeff také zařazuje všechny své stávající místní servery, které ještě nebyly migrovány do cloudu, aby mohly využívat hybridní podporu Security Center na svých serverech [windows](quick-onboard-windows-computer.md) a [linux.](quick-onboard-linux-computer.md)

Jeff je vlastník cloudové úlohy. Jeff je zodpovědný za použití bezpečnostních kontrol v souladu se zásadami zabezpečení společnosti Contoso. 

Jan provádí následující úkoly:

- Sledování bezpečnostních doporučení poskytovaných centrem zabezpečení
- Vyhodnoťte doporučení zabezpečení a rozhodněte se, zda mají doporučení použít, nebo je zamítnout.
- Použití doporučení zabezpečení

### <a name="remediate-threats-using-recommendations"></a>Náprava hrozeb pomocí doporučení
Jako součást svých každodenních aktivit monitorování se Jeff přihlásí do Azure a otevře Centrum zabezpečení. 

1. Jeff vybere odběry úlohy.

2. Jeff zkontroluje **skóre zabezpečení,** aby získal celkový přehled o tom, jak bezpečná jsou předplatná, a zjistí, že skóre je 548.

3. Jeff se musí rozhodnout, která doporučení se mají zpracovat jako první. Takže Jeff klikne Secure Score a začne zpracovávat doporučení na základě toho, jak moc to zlepšuje jeho [Secure Score dopad](security-center-secure-score.md).

4. Vzhledem k tomu, že Jeff má spoustu připojených virtuálních počítačů a serverů, jeff se rozhodne zaměřit se na **výpočetní výkon a aplikace**.

5. Když Jeff klikne na **Výpočetní výkon a aplikace**, zobrazí se jim seznam doporučení a zřídí je podle dopadu na zabezpečené skóre.

6. Jeff má mnoho internetových virtuálních aplikací a protože jejich porty jsou vystaveny, obávají se, že by útočník mohl získat kontrolu nad servery. Takže Jeff se rozhodne použít [**přístup k virtuálním**](security-center-just-in-time.md)ms za chvíli .

Jeff pokračuje v pohybu prostřednictvím doporučení s vysokou prioritou a střední prioritou a rozhoduje o implementaci. Pro každé doporučení, Jeff se dívá na podrobné informace poskytované Security Center pochopit, které prostředky jsou ovlivněny, co je zabezpečené skóre dopad, co každé doporučení znamená a nápravné kroky pro zmírnění jednotlivých problémů.

## <a name="conclusion"></a>Závěr
Doporučení pro monitorování v Centru zabezpečení vám pomohou odstranit slabá místa zabezpečení dříve, než dojde k útoku. Když napomáháte doporučením, vaše skóre zabezpečení a stav zabezpečení úloh se zlepší. Security Center automaticky zjišťuje nové prostředky, které nasazujete, vyhodnocuje je podle zásad zabezpečení a poskytuje nová doporučení pro jejich zabezpečení.


## <a name="next-steps"></a>Další kroky
Ujistěte se, že máte monitorovací proces na místě, ve kterém pravidelně kontrolovat doporučení v Centru zabezpečení, takže můžete zajistit, aby vaše prostředky v bezpečí v průběhu času.

Tento scénář vám ukázal, jak používat zásady zabezpečení a doporučení v Centru zabezpečení ke zmírnění útoku zabezpečení.

Zjistěte, jak reagovat na hrozby [pomocí správy a reakce na výstrahy zabezpečení](security-center-managing-and-responding-alerts.md).

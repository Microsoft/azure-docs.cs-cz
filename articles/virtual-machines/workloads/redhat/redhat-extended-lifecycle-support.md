---
title: Rozšířená podpora životního cyklu pro Red Hat Enterprise Linux
description: Další informace o přidání podpory pro rozšíření Red Hat Enterprise Extended životní cyklus
author: mathapli
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.topic: article
ms.date: 04/16/2020
ms.author: mathapli
ms.openlocfilehash: 703732725ae7215d3ff59ad92a4c171a86251c20
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101677197"
---
# <a name="red-hat-enterprise-linux-rhel-extended-lifecycle-support"></a>Rozšířená podpora životního cyklu Red Hat Enterprise Linux (RHEL)
Tento článek poskytuje informace o podpoře rozšířeného životního cyklu pro Image Red Hat Enterprise:
* Red Hat Enterprise Linux 6  

## <a name="red-hat-enterprise-linux-6-lifecycle"></a>Životní cyklus Red Hat Enterprise Linux 6
Od 30. listopadu 2020 se Red Hat Enterprise Linux 6 dostane ke konci fáze údržby. Fáze údržby následuje po rozšířené životní fázi. Vzhledem k tomu, že Red Hat Enterprise Linux 6 přechody mimo fáze úplné/údržby, důrazně doporučujeme upgradovat na Red Hat Enterprise Linux 7 nebo 8. Pokud si zákazníci musí zůstat v Red Hat Enterprise Linux 6, doporučujeme přidat doplněk podpora rozšíření ELS (Extended životní cyklus) Red Hat Enterprise Linux.

## <a name="steps-to-add-extended-lifecycle-support-on-marketplace-pay-as-you-go-vms"></a>Postup přidání rozšířené podpory životního cyklu na virtuální počítače s průběžnými platbami podle aktuálního využití na webu Marketplace
1. Vyplňte formulář ELS, který je [k dispozici](https://aka.ms/els-form) , pomocí informací o kontaktu a informací o předplatném virtuálních počítačů, pro které chcete přidat podporu ELS. Podrobnosti o cenách pro doplňky jsou k dispozici také ve formuláři.
1. Tým Azure Red Hat Enterprise Linux vás bude kontaktovat a seznam virtuálních počítačů pro podporu ELS se přidávají do 1-2 pracovních dnů. Seznamte se prosím se seznamem a vyjádřete souhlas s přidáním cen.
1. Tým Azure Red Hat Enterprise Linux bude sdílet kroky pro přidání balíčku klienta ELS do virtuálních počítačů. Postupujte podle kroků, které budou k dispozici v e-mailu, abyste mohli dál přijímat údržbu softwaru (opravy chyb a zabezpečení) a podporu Red Hat Enterprise Linux 6.

> [!Note]
> Nesdílejte kroky pro použití RHEL ELS přidat k komukoli mimo vaši organizaci. AzureRedHatELS@microsoft.comVyužijte k získání podpory nebo pro všechny další otázky.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

#### <a name="im-running-red-hat-enterprise-linux-6-and-cant-migrate-to-a-later-version-at-this-time-what-options-do-i-have"></a>Používám Red Hat Enterprise Linux 6 a v tuto chvíli se nedá migrovat na novější verzi. Jaké možnosti mám?
* Nadále spouštějte Red Hat Enterprise Linux 6 a Zakupte si podporu rozšířeného životního cyklu (ELS) Add-On úložišť, aby se nadále přijímala omezená údržba softwaru, a technickou podporu (viz postup upgradu a podrobnosti o cenách níže).
* Migrujte na Red Hat Enterprise Linux 7 nebo 8, jakmile budete moct.

#### <a name="what-is-the-additional-charge-for-using-red-hat-enterprise-linux-extended-life-cycle-support-els-add-on"></a>Jaké jsou další poplatky za používání doplňku Red Hat Enterprise Linux rozšířené podpory životního cyklu (ELS)?
Náklady související s rozšířenou podporou životního cyklu najdete ve [formuláři ELS](https://aka.ms/els-form)

#### <a name="ive-deployed-a-vm-by-using-custom-image-how-can-i-add-extended-lifecycle-support-to-this-vm"></a>Nasadil (a) jsem virtuální počítač s použitím vlastní image. Jak můžu přidat rozšířenou podporu pro životní cyklus do tohoto virtuálního počítače?
Musíte se obrátit přímo na Red Hat a získat podporu přímo z nich.

#### <a name="ive-deployed-a-vm-by-using-custom-image-can-i-convert-this-vm-to-a-payg-vm"></a>Nasadil (a) jsem virtuální počítač s použitím vlastní image. Můžu tento virtuální počítač převést na virtuální počítač s PAYG?
Ne, nemůžete. V současnosti se převod nepodporuje v Azure.


## <a name="next-steps"></a>Další kroky

* Pokud chcete zobrazit úplný seznam imagí RHEL v Azure, přečtěte si část [Red Hat Enterprise Linux (RHEL) imagí dostupné v Azure](./redhat-imagelist.md).
* Další informace o infrastruktuře aktualizací Red Hat najdete v tématu [infrastruktura aktualizace Red Hat pro virtuální počítače RHEL na vyžádání v Azure](./redhat-rhui.md).
* Další informace o nabídce BYOS pro RHEL Red Hat Enterprise Linux najdete v článku o tom, jak se v Azure naučíte používat [zlaté image pro vlastní odběry](./byos.md).
* Informace o zásadách podpory Red Hat pro všechny verze RHEL najdete v tématu [Red Hat Enterprise Linux životní cyklus](https://access.redhat.com/support/policy/updates/errata).
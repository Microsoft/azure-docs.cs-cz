---
title: Přehled ověřování jako služba pro službu Azure Stack | Dokumentace Microsoftu
description: Přehled ověřování služby Azure Stack jako služba – známé problémy.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: c96e7385356354d398108ad69492603d38667e46
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2018
ms.locfileid: "40234884"
---
# <a name="what-is-validation-as-a-service-for-azure-stack"></a>Co je ověření jako služba pro službu Azure Stack?

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Ověření jako služba (VaaS) je navržená pro řešení partnerů, kteří jsou společně technické nabídek Azure Stack s Microsoftem nativní služby Azure. Partneři řešení můžete použít službu Pokud chcete zkontrolovat, že svá řešení požadavkům Microsoftu na a fungovat podle očekávání pomocí služby Azure Stack.

Je primárně používají k VaaS:

- Ověření nových řešení Azure Stack
- Ověření změn softwaru Azure Stack
- Získat partnera řešení digitálně podepsané balíčky používané během nasazení
- Zajištění ověřování ve verzi Preview služby Azure Stack

## <a name="validate-new-azure-stack-solution"></a>Ověření nové řešení Azure Stack

Partneři použijte pracovní postup ověření řešení ke kontrole nová řešení Azure Stack. Řešení musí projít testy komponenty požadované testy Hardware Lab Kit (HKL) Azure Stack. Potřebujete spustit pracovní postup pro každé nové řešení dvakrát: jednou pro konfiguraci minimální a maximální.

Další informace najdete v tématu [ověřit nové řešení Azure Stack](azure-stack-vaas-validate-solution-new.md).

## <a name="validate-changes-to-the-azure-stack-software"></a>Ověření změn softwaru Azure Stack

Partneři pomocí pracovní postup ověření balíčku zkontrolujte, že jejich řešení funguje s nejnovější aktualizací softwaru Azure Stack. Minimálně pracovní postup ověření balíčku musí být spuštěn v prostředí hardwaru, kterou společnost Microsoft doporučuje a na řešení kde opravy a aktualizace (P & U) byly použity k instalaci aktualizace. Ověření balíčku by měl spouštět sestavení směrného plánu.

Další informace najdete v tématu [ověření aktualizace softwaru od Microsoftu](azure-stack-vaas-validate-microsoft-updates.md).

## <a name="get-digitally-signed-solution-partner-packages"></a>Získání řešení digitálně podepsané balíčky partnera

Kromě ověřování služby Azure Stack aktualizace, můžete použít pracovní postup ověření balíček ke kontrole aktualizací pro výrobce OEM přizpůsobení balíčky, mezi které patří Azure Stack partnera konkrétní ovladače, firmware a další software používá při nasazení služby Azure Stack software. Nasazení balíčku, který se díváte na aktuální verzi služby Azure Stack pomocí alespoň minimální velikosti řešení, která bude podporovat. Aktualizovaný balíček musí být odeslán do služby před spuštěním testu. Pokud testy úspěšné, upozorní vaashelp@microsoft.com. Partner Azure Stack říct, že balíček dokončil testování a by měl být digitálně podepsané digitálním podpisem Azure Stack. Microsoft podepíše balíček a upozorní partnera Azure Stack, že balíček je k dispozici ke stažení na portálu.

Další informace najdete v tématu [ověřit OEM balíčky](azure-stack-vaas-validate-oem-package.md).

## <a name="preview-azure-stack-validation-collateral"></a>Zajištění ověřování ve verzi Preview služby Azure Stack

Microsoft pravidelně zpřístupňuje nové funkce ve službě Azure Stack. Jako součást procesu vývoje pro doručování tyto funkce na trh zajištění nového testu je k dispozici v pracovním postupu průchodu testu. Pracovní postup průchodu testu obsahuje testovací zajištění z jiných pracovních postupů umožňující provádění neoficiální testů. Nepoužívejte pracovního postupu průchodu testů odeslat výsledky ke schválení. Pomocí řešení ověření a balíček ověření pracovního postupu získáte oficiální schválení pro vaše řešení.

## <a name="next-steps"></a>Další postup

- Začněte tím, a [nastavení ověření jako účet služby](azure-stack-vaas-validate-solution-new.md)

---
title: Omezení přístupu prostřednictvím internetových koncových bodů v Azure Security Center | Microsoft Docs
description: Tento dokument se dozvíte, jak provést doporučení Azure Security Center **omezit přístup prostřednictvím internetové koncový bod**.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 727d88c9-163b-4ea0-a4ce-3be43686599f
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2017
ms.author: terrylan
ms.openlocfilehash: 92906d31f4db21f37094f192dadd080e28cc6e8e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34363049"
---
# <a name="restrict-access-through-internet-facing-endpoints-in-azure-security-center"></a>Omezení přístupu prostřednictvím internetových koncových bodů v Azure Security Center
Azure Security Center doporučí, omezení přístupu prostřednictvím internetových koncových bodů, pokud žádné skupiny zabezpečení sítě (Nsg) má jednu nebo více příchozí pravidla, která umožňují přístup z "žádné" zdrojové IP adresy. Otevírání přístup k "žádné" může povolit útočníci k přístupu k prostředkům. Security Center doporučí, že upravíte tyto příchozích pravidel pro omezení přístupu ke zdrojové IP adresy, které skutečně potřebují přístup.

Toto doporučení se generuje pro všechny port jiný web, který má "žádný" jako zdroj.

> [!NOTE]
> Tento dokument vám tuto službu představí formou ukázkového nasazení. Není to podrobný průvodce.
>
>

## <a name="implement-the-recommendation"></a>Implementace doporučení
1. V **doporučení okno**, vyberte **omezit přístup prostřednictvím internetové koncový bod**.

   ![Omezit přístup přes internetový koncový bod][1]
2. Otevře se okno pro **omezit přístup prostřednictvím internetové koncový bod**. Toto okno Seznam virtuálních počítačů (VM) s příchozích pravidel, které vytvářejí potenciálním potížím se zabezpečením. Vyberte virtuální počítač.

   ![Vyberte virtuální počítač][2]
3. **NSG** zobrazuje informace skupinu zabezpečení sítě, související příchozích pravidel a přidružené virtuální počítač. Vyberte **upravit příchozí pravidla** pokračujte úpravy příchozího pravidla.

   ![Okno skupina zabezpečení sítě][3]
4. Na **příchozí pravidla zabezpečení** vyberte příchozí pravidlo, které chcete upravit. V tomto příkladu budeme vyberte **AllowWeb**.

   ![Příchozí pravidla zabezpečení][4]

   Všimněte si, můžete také vybrat **výchozí pravidla** zobrazíte sadu výchozích pravidel obsažené ve všech skupin Nsg. Výchozí pravidla nelze odstranit, ale protože je jim přiřazená s nižší prioritou, dají se přepsat pravidly, které vytvoříte. Další informace o [výchozí pravidla](../virtual-network/security-overview.md#default-security-rules).

   ![Výchozí pravidla][5]
5. Na **AllowWeb** okně Upravit vlastnosti příchozí pravidlo tak, aby **zdroj** je IP adresa nebo blok IP adres. Další informace o vlastnostech příchozí pravidlo, najdete v části [pravidla NSG](../virtual-network/security-overview.md#security-rules).

   ![Upravit pravidlo pro příchozí][6]

## <a name="see-also"></a>Další informace najdete v tématech
Tento článek vám ukázal, jak provést doporučení Security Center "Omezit přístup prostřednictvím internetový koncový bod." Další informace o povolení skupiny Nsg a pravidla, naleznete v následujících tématech:

* [Co je skupina zabezpečení sítě (NSG)?](../virtual-network/security-overview.md)
* [Spravovat skupinu zabezpečení sítě](../virtual-network/manage-network-security-group.md)

Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení v Azure Security Center](security-center-policies.md) – Zjistěte, jak se konfigurují zásady zabezpečení pro vaše předplatné Azure a skupiny prostředků.
* [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md) – Zjistěte, jak vám doporučení pomáhají chránit prostředky v Azure.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se monitorovat stav svých prostředků Azure.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Sledování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md) – Zjistěte, jak pomocí Azure Security Center sledovat stav vašich partnerských řešení.
* [Azure Security Center – nejčastější dotazy](security-center-faq.md) – Přečtěte si nejčastější dotazy o použití této služby.
* [Blog Azure Security](http://blogs.msdn.com/b/azuresecurity/) – Získejte nejnovější informace o zabezpečení Azure.

<!--Image references-->
[1]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/restrict-access-thru-internet-facing-endpoint.png
[2]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/select-a-vm.png
[3]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/network-security-group-blade.png
[4]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/inbound-security-rules.png
[5]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/default-rules.png
[6]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/edit-inbound-rule.png

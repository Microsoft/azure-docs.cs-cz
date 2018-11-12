---
title: Omezení přístupu prostřednictvím internetových koncových bodů v Azure Security Center | Dokumentace Microsoftu
description: Tento dokument popisuje, jak implementovat doporučení služby Azure Security Center **omezit přístup přes internetový koncový bod**.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 727d88c9-163b-4ea0-a4ce-3be43686599f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 8e404651fa315f630ff190c9e70ccdd3eec7f117
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51229998"
---
# <a name="restrict-access-through-internet-facing-endpoints-in-azure-security-center"></a>Omezení přístupu prostřednictvím internetových koncových bodů v Azure Security Center
Azure Security Center vám doporučí omezení přístupu prostřednictvím internetových koncových bodů, pokud žádné skupiny zabezpečení sítě (Nsg) obsahuje jeden nebo více příchozí pravidla, která umožňují přístup z "libovolné" zdrojové IP adresy. Otevírání přístup k "žádný" může umožnit útočníkům získat přístup k vašim prostředkům. Security Center vám doporučí, abyste upravili tato příchozích pravidel pro omezení přístupu k zdrojové IP adresy, které skutečně potřebují mít přístup.

Toto doporučení se generuje pro jakýkoli port mimo web "žádný" má jako zdroj.

> [!NOTE]
> Tento dokument vám tuto službu představí formou ukázkového nasazení. Není to podrobný průvodce.
>
>

## <a name="implement-the-recommendation"></a>Implementace doporučení
1. V **okno doporučení**vyberte **omezit přístup přes internetový koncový bod**.

   ![Omezit přístup přes internetový koncový bod][1]
2. Otevře se okno **omezit přístup přes internetový koncový bod**. Toto okno Seznam virtuálních počítačů (VM) s příchozí pravidla, které vytváří potenciální problém zabezpečení. Vyberte virtuální počítač.

   ![Vyberte virtuální počítač][2]
3. **NSG** okně se zobrazí informace o skupině zabezpečení sítě, související příchozí pravidla a přidružený virtuální počítač. Vyberte **upravit příchozí pravidla** pokračujte s úpravami příchozí pravidlo.

   ![Okno skupiny zabezpečení sítě][3]
4. Na **příchozí pravidla zabezpečení** okně vyberte příchozí pravidlo, které chcete upravit. V tomto příkladu vybereme **AllowWeb**.

   ![Příchozí pravidla zabezpečení][4]

   Mějte na paměti, můžete také vybrat **výchozí pravidla** zobrazíte sadu výchozích pravidel, které jsou obsaženy ve všech skupin zabezpečení sítě. Výchozí pravidla nejde odstranit, ale protože je jim přiřazená s nižší prioritou, dají se přepsat pravidly, která vytvoříte. Další informace o [výchozí pravidla](../virtual-network/security-overview.md#default-security-rules).

   ![Výchozí pravidla][5]
5. Na **AllowWeb** okně Upravit vlastnosti příchozí pravidlo tak, aby **zdroj** je IP adresa nebo blok IP adres. Další informace o vlastnostech příchozí pravidlo, najdete v článku [pravidla NSG](../virtual-network/security-overview.md#security-rules).

   ![Upravit příchozí pravidla][6]

## <a name="see-also"></a>Další informace najdete v tématech
Tento článek vám ukázali, jak implementovat doporučení služby Security Center "Omezit přístup přes internetový koncový bod." Další informace o povolení skupin zabezpečení sítě a pravidel, naleznete v následujících tématech:

* [Co je skupina zabezpečení sítě (NSG)?](../virtual-network/security-overview.md)
* [Spravovat skupiny zabezpečení sítě](../virtual-network/manage-network-security-group.md)

Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení v Azure Security Center](security-center-policies.md) – Zjistěte, jak se konfigurují zásady zabezpečení pro vaše předplatné Azure a skupiny prostředků.
* [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md) – Zjistěte, jak vám doporučení pomáhají chránit prostředky v Azure.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se monitorovat stav svých prostředků Azure.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Sledování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md) – Zjistěte, jak pomocí Azure Security Center sledovat stav vašich partnerských řešení.
* [Azure Security Center – nejčastější dotazy](security-center-faq.md) – Přečtěte si nejčastější dotazy o použití této služby.
* [Blog Azure Security](https://blogs.msdn.com/b/azuresecurity/) – Získejte nejnovější informace o zabezpečení Azure.

<!--Image references-->
[1]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/restrict-access-thru-internet-facing-endpoint.png
[2]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/select-a-vm.png
[3]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/network-security-group-blade.png
[4]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/inbound-security-rules.png
[5]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/default-rules.png
[6]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/edit-inbound-rule.png

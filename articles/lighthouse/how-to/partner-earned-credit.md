---
title: Propojte své ID partnera a sledujte svůj dopad na delegovaných prostředcích.
description: Přečtěte si, jak přidružit ID partnera pro příjem kreditu (PEC) pro zákaznické prostředky, které spravujete prostřednictvím Azure Lighthouse.
ms.date: 02/12/2021
ms.topic: how-to
ms.openlocfilehash: 4c18aae38570ab3fd84df7d45fb18e35404158be
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100372089"
---
# <a name="link-your-partner-id-to-track-your-impact-on-delegated-resources"></a>Propojte své ID partnera a sledujte svůj dopad na delegovaných prostředcích. 

Pokud jste členem [Microsoft Partner Network](https://partner.microsoft.com/), můžete propojit ID partnera s přihlašovacími údaji, které jste použili ke správě delegovaných prostředků zákazníků, a umožnit tak Microsoftu identifikovat a rozpoznávat partnery, kteří chtějí zákazníky v Azure řídit. Toto propojení také umožňuje partnerům [CSP (Cloud Solution Provider)](/partner-center/csp-overview) získávat [kredit pro partnery (PEC) za spravované služby](/partner-center/partner-earned-credit) a zákazníky, kteří [podepsali Smlouvu se zákazníkem Microsoftu (MCA)](/partner-center/confirm-customer-agreement) a jsou [v rámci daného plánu Azure](/partner-center/azure-plan-get-started).

Pokud chcete získat rozpoznávání pro aktivity Azure Lighthouse, musíte [propojit ID MPN](../../cost-management-billing/manage/link-partner-id.md) s alespoň jedním uživatelským účtem ve vašem tenantovi a zajistit, aby měl propojený účet přístup ke každému z vašich poregistračních předplatných.

## <a name="associate-your-partner-id-when-you-onboard-new-customers"></a>Přidružte své ID partnera při připojování nových zákazníků.

Pomocí následujícího postupu můžete propojit ID partnera (Pokud je to možné), a povolit získaný kredit partnerských procesů. K provedení těchto kroků budete muset znát [ID partnera MPN](/partner-center/partner-center-account-setup#locate-your-mpn-id) . Nezapomeňte použít **přidružené ID MPN**, které je uvedené ve vašem partnerském profilu.

Pro zjednodušení doporučujeme vytvořit v tenantovi účet instančního objektu, propojit ho s **přidruženým ID MPN** a pak mu udělit přístup ke každému zákazníkovi, který zařadíte pomocí [předdefinované role Azure, která má nárok na pec](/partner-center/azure-roles-perms-pec).

1. [Vytvořte uživatelský účet instančního objektu](../../active-directory/develop/howto-authenticate-service-principal-powershell.md) ve vašem tenantovi pro správu. V tomto příkladu použijeme pro tento hlavní účet služby *účet automatizace poskytovatele* názvů.
1. Pomocí tohoto účtu instančního objektu se v tenantovi [pro správu připojte k přidruženému ID MPN](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id) . Stačí to udělat jenom jednou.
1. Když připojíte zákazníka [pomocí šablon ARM](onboard-customer.md) nebo [nabídek spravovaných služeb](publish-managed-services-offers.md), nezapomeňte zahrnout autorizaci, která zahrnuje účet služby Automation Provider jako uživatel s [integrovanou rolí Azure, která má nárok na řadič pro](/partner-center/azure-roles-perms-pec)správu základní desky.

Pomocí těchto kroků se každý tenant zákazníka, který spravujete, přidruží k vašemu ID partnera. Účet Automation zprostředkovatele nemusí v tenantovi zákazníka ověřovat ani provádět žádné akce.

:::image type="content" source="../media/lighthouse-pal.jpg" alt-text="Diagram znázorňující proces propojení ID partnera s Azure Lighthouse.":::

## <a name="add-your-partner-id-to-previously-onboarded-customers"></a>Přidat ID partnera k dříve připojeným zákazníkům

Pokud jste již provedli registraci zákazníka, nebudete pravděpodobně chtít provést jiné nasazení, aby bylo možné přidat instanční objekt pro účet Automation Provider. Místo toho můžete propojit **přidružené ID MPN** s uživatelským účtem, který už má přístup k práci v tenantovi daného zákazníka. Ujistěte se, že má účet přiřazenou [předdefinovanou roli Azure, která má nárok na pec](/partner-center/azure-roles-perms-pec).

Jakmile je účet [propojený s vaším přidruženým ID MPN](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id) ve vašem tenantovi pro správu, budete moct sledovat jeho dopad na svůj dopad na daného zákazníka.

## <a name="confirm-partner-earned-credit"></a>Potvrďte kredit získaný partnerem

Podrobnosti o [řadiči pec můžete zobrazit v Azure Portal](/partner-center/partner-earned-credit-explanation#azure-cost-management) a potvrdit, které náklady na řadič PEC dostaly. Pamatujte, že PEC se vztahuje jenom na zákazníky CSP, kteří si zaregistrovali MCA a jsou v rámci plánu Azure.

Pokud jste postupovali podle výše uvedených kroků a nevidíte očekávané přidružení, otevřete žádost o podporu v Azure Portal.

K automatizaci ověřování PEC pro předplatné můžete také použít [sadu SDK partnerského centra](/partner-center/develop/get-invoice-unbilled-consumption-lineitems) a filtrovat `rateOfPartnerEarnedCredit` .

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [Microsoft Partner Network](/partner-center/mpn-overview).
- Přečtěte si [, jak se vypočítává a vyplácí pec](/partner-center/partner-earned-credit-explanation).
- Zaveďte [zákazníky](onboard-customer.md) do Azure Lighthouse.
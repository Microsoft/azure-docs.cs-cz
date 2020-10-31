---
title: Propojte své ID partnera a sledujte svůj dopad na delegovaných prostředcích.
description: Přečtěte si, jak přidružit ID partnera pro příjem kreditu (PEC) pro zákaznické prostředky, které spravujete prostřednictvím Azure Lighthouse.
ms.date: 10/30/2020
ms.topic: how-to
ms.openlocfilehash: fcbcc70e380116b8e9f9b1c1e365dee1adb87a99
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93080273"
---
# <a name="link-your-partner-id-to-track-your-impact-on-delegated-resources"></a>Propojte své ID partnera a sledujte svůj dopad na delegovaných prostředcích. 

Pokud jste členem [Microsoft Partner Network](https://partner.microsoft.com/), můžete propojit ID partnera s přihlašovacími údaji použitými ke správě prostředků delegovaných zákazníků. Odkaz pro správce pro partnery (PAL) umožňuje Microsoftu identifikovat a rozpoznávat partnery, kteří dodají zákazníky Azure na úspěch. Tento odkaz taky umožňuje partnerům [CSP (Cloud Solution Provider)](/partner-center/csp-overview) přijímat [kredity s získanými partnery pro spravované služby (PEC)](/partner-center/partner-earned-credit) pro zákazníky, kteří [podepsali smlouvu o zákaznících Microsoftu (MCA)](/partner-center/confirm-customer-agreement) a jsou [v rámci plánu Azure](/partner-center/azure-plan-get-started).

Pokud připojíte [zákazníky k nabídkám spravovaných služeb v Azure Marketplace](publish-managed-services-offers.md), propojí se automaticky pomocí ID MPN přidruženého k účtu partnerského centra používaného k publikování nabídek. Žádná další akce není nutná k tomu, aby bylo možné sledovat váš dopad na tyto zákazníky.

Pokud připojíte [zákazníky pomocí šablon správy prostředků Azure](onboard-customer.md), budete muset provést akci, abyste tento odkaz vytvořili. Provedete to tak, že [propojíte ID MPN](../../cost-management-billing/manage/link-partner-id.md) s alespoň jedním uživatelským účtem ve vašem tenantovi pro správu, který má přístup ke každému z vašich poregistračních předplatných.

## <a name="associate-your-partner-id-when-you-onboard-new-customers"></a>Přidružte své ID partnera při připojování nových zákazníků.

Při připojování zákazníků prostřednictvím šablon Azure Resource Manager (šablony ARM) použijte následující postup, který propojí ID partnera (a pokud je k dispozici možnost Povolit získaný partnerský kredit). K provedení těchto kroků budete muset znát [ID partnera MPN](/partner-center/partner-center-account-setup#locate-your-mpn-id) . Nezapomeňte použít **přidružené ID MPN** , které je uvedené ve vašem partnerském profilu.

Pro zjednodušení doporučujeme vytvořit v tenantovi účet instančního objektu, propojit ho s **přidruženým ID MPN** a pak mu udělit přístup ke každému zákazníkovi, který zařadíte pomocí [předdefinované role Azure, která má nárok na pec](/partner-center/azure-roles-perms-pec).

1. Ve vašem tenantovi pro správu [Vytvořte hlavní účet služby](../../active-directory/develop/howto-authenticate-service-principal-powershell.md) . V tomto příkladu použijeme *účet automatizace poskytovatele* názvu pro tento instanční objekt.
1. Pomocí tohoto účtu instančního objektu se v tenantovi [pro správu připojte k přidruženému ID MPN](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id) . Stačí to udělat jenom jednou.
1. Při [připojování zákazníka pomocí šablon ARM](onboard-customer.md)nezapomeňte zahrnout autorizaci, která zahrnuje účet Automation zprostředkovatele jako uživatel s [integrovanou rolí Azure, která má nárok na řadič PEC](/partner-center/azure-roles-perms-pec).

Pomocí těchto kroků se každý tenant zákazníka, který spravujete, přidruží k vašemu ID partnera. Účet Automation zprostředkovatele nemusí v tenantovi zákazníka ověřovat ani provádět žádné akce.

:::image type="content" source="../media/lighthouse-pal.jpg" alt-text="Diagram znázorňující proces PAL s Azure Lighthouse":::

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
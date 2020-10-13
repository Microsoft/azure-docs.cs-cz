---
title: Propojte své ID partnera, aby bylo možné u delegovaných prostředků povolit kredit získaný pro partnery.
description: Přečtěte si, jak přidružit ID partnera pro příjem kreditu (PEC) pro zákaznické prostředky, které spravujete prostřednictvím Azure Lighthouse.
ms.date: 10/05/2020
ms.topic: how-to
ms.openlocfilehash: d9d80a94e52f6f6a8aef5f5284659750084b0b5e
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91974443"
---
# <a name="link-your-partner-id-to-enable-partner-earned-credit-on-delegated-resources"></a>Propojte své ID partnera, aby bylo možné u delegovaných prostředků povolit kredit získaný pro partnery.

Pokud jste členem [Microsoft Partner Network](https://partner.microsoft.com/), můžete propojit ID partnera s přihlašovacími údaji použitými ke správě prostředků delegovaných zákazníků. Díky tomu můžete sledovat svůj dopad napříč zapojením zákazníků a přijímat [kredity získané partnery pro spravované služby (PEC)](/partner-center/partner-earned-credit).

Pokud zapojíte [zákazníky se nabídkami spravované služby v Azure Marketplace](publish-managed-services-offers.md), bude se tento odkaz automaticky provádět pomocí ID MPN přidruženého k účtu partnerského centra používaného k publikování nabídek. Pro příjem řadičů PEC pro tyto zákazníky není nutná žádná další akce.

Pokud připojíte [zákazníky pomocí šablon správy prostředků Azure](onboard-customer.md), budete muset provést akci, abyste tento odkaz vytvořili. Provedete to tak, že [propojíte ID MPN](../../cost-management-billing/manage/link-partner-id.md) s alespoň jedním uživatelským účtem ve vašem tenantovi pro správu, který má přístup ke každému z vašich poregistračních předplatných.

## <a name="associate-your-partner-id-when-you-onboard-new-customers"></a>Přidružte své ID partnera při připojování nových zákazníků.

Při připojování zákazníků prostřednictvím šablon Azure Resource Manager (šablony ARM) použijte následující postup, který propojí ID partnera a povolí získaný kredit pro partnery. K provedení těchto kroků budete muset znát [ID partnera MPN](/partner-center/partner-center-account-setup#locate-your-mpn-id) . Nezapomeňte použít **přidružené ID MPN**, které je uvedené ve vašem partnerském profilu.

Pro zjednodušení doporučujeme vytvořit v tenantovi účet instančního objektu, propojit ho s **přidruženým ID MPN**a pak mu udělit přístup ke každému zákazníkovi, který zařadíte pomocí [předdefinované role Azure, která má nárok na pec](/partner-center/azure-roles-perms-pec).

1. Ve vašem tenantovi pro správu [Vytvořte hlavní účet služby](../../active-directory/develop/howto-authenticate-service-principal-powershell.md) . V tomto příkladu pojmenujte tento účet služby PEC automatizace pro instanční službu.
1. Pomocí tohoto účtu instančního objektu se v tenantovi [pro správu připojte k přidruženému ID MPN](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id) . Stačí to udělat jenom jednou.
1. Při [připojování zákazníka pomocí šablon ARM](onboard-customer.md)nezapomeňte zahrnout autorizaci, která zahrnuje účet služby pec pro Azure jako uživatel s [integrovanou rolí Azure, která má nárok na řadič PEC](/partner-center/azure-roles-perms-pec).

Pomocí těchto kroků se každý tenant zákazníka bude přidružit k vašemu ID partnera, což vám umožní přijímat pro tyto zákazníky řadiče PEC. Účet služby PEC pro řadič PEC nemusí ověřovat ani provádět žádné akce v tenantovi zákazníka.

## <a name="add-your-partner-id-to-previously-onboarded-customers"></a>Přidat ID partnera k dříve připojeným zákazníkům

Pokud jste již provedli připojení k zákazníkovi, nebudete pravděpodobně chtít provést jiné nasazení, abyste přidali instanční objekt pro účet služby PEC Automation. Místo toho můžete propojit **přidružené ID MPN** s uživatelským účtem, který už má přístup k práci v tenantovi daného zákazníka. Ujistěte se, že má účet přiřazenou [předdefinovanou roli Azure, která má nárok na pec](/partner-center/azure-roles-perms-pec).

Jakmile je účet [propojený s vaším přidruženým ID MPN](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id) ve vašem tenantovi pro správu, budete moct pro tohoto zákazníka získat pec.

## <a name="confirm-partner-earned-credit"></a>Potvrďte kredit získaný partnerem

Podrobnosti o [řadiči pec můžete zobrazit v Azure Portal](/partner-center/partner-earned-credit-explanation#azure-cost-management) a potvrdit, které náklady na řadič PEC dostaly.

Pokud jste postupovali podle výše uvedených kroků a nevidíte přidružení, otevřete žádost o podporu v Azure Portal.

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [Microsoft Partner Network](/partner-center/mpn-overview).
- Přečtěte si [, jak se vypočítává a vyplácí pec](/partner-center/partner-earned-credit-explanation).
- Zaveďte [zákazníky](onboard-customer.md) do Azure Lighthouse.
---
title: Zobrazení a Správa zákazníků a delegovaných prostředků v Azure Portal
description: Jako poskytovatel služeb nebo podniková služba s využitím Azure Lighthouse můžete zobrazit všechny delegované prostředky a odběry tak, že v Azure Portal kliknete na moji zákazníci.
ms.date: 03/12/2021
ms.topic: how-to
ms.openlocfilehash: 78344015ee027b9844b6339fa7cd95d348488a54
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2021
ms.locfileid: "103419325"
---
# <a name="view-and-manage-customers-and-delegated-resources-in-the-azure-portal"></a>Zobrazení a Správa zákazníků a delegovaných prostředků v Azure Portal

Poskytovatelé služeb používající [Azure Lighthouse](../overview.md) můžou pomocí stránky **moji zákazníci** v [Azure Portal](https://portal.azure.com) zobrazit delegované prostředky a předplatná zákazníků.

> [!TIP]
> V takovém případě budeme odkazováni na poskytovatele služeb a zákazníky a podniky, které [spravují víc tenantů](../concepts/enterprise.md) , můžou stejný postup využít k konsolidaci prostředí pro správu.

Chcete-li získat přístup ke stránce **moji zákazníci** v Azure Portal, vyberte **všechny služby** a pak vyhledejte **Moje zákazníky** a vyberte ji. Můžete ji také najít zadáním "Moji zákazníci" do vyhledávacího pole v horní části Azure Portal.

Mějte na paměti, že v části hlavní **zákazníci** na stránce **moji zákazníci** se zobrazují jenom informace o zákaznících, kteří mají delegované předplatné nebo skupiny prostředků pro vašeho TENANTA Azure Active Directory (Azure AD) prostřednictvím Azure Lighthouse. Pokud pracujete s ostatními zákazníky (například prostřednictvím [programu Cloud Solution Provider (CSP)](/partner-center/csp-overview)), neuvidíte informace o těchto zákaznících v části **zákazníci** , pokud jste nepřipojili [své prostředky k Azure Lighthouse](onboard-customer.md) (přestože se vám můžou zobrazit podrobnosti o určitých zákaznících CSP v [části Cloud Solution Provider (Preview)](#cloud-solution-provider-preview) na stránce).

> [!NOTE]
> Vaši zákazníci mohou zobrazit informace o poskytovatelích služeb přechodem na **poskytovatele služeb** v Azure Portal. Další informace najdete v tématu [zobrazení a Správa poskytovatelů služeb](view-manage-service-providers.md).

## <a name="view-and-manage-customer-details"></a>Zobrazit a spravovat podrobnosti o zákazníkovi

Pokud chcete zobrazit podrobnosti o zákaznících, vyberte na levé straně stránky **Customers** možnost **zákazníci** .

> [!IMPORTANT]
> Aby bylo možné tyto informace zobrazit, uživatelé musí mít v procesu připojování udělenou roli [Čtenář](../../role-based-access-control/built-in-roles.md#reader) (nebo jinou předdefinovanou roli, která zahrnuje přístup ke čtenářům).

Pro každého zákazníka uvidíte jméno zákazníka, ID zákazníka (ID tenanta) a **ID nabídky** a **verzi nabídky** přidruženou ke službě Engagement. Ve sloupci **delegování** se zobrazí počet delegovaných předplatných nebo počet delegovaných skupin prostředků.

Možnosti v horní části stránky umožňují řadit, filtrovat a seskupovat informace o zákaznících konkrétními zákazníky, nabídkami nebo klíčovými slovy.

Na této stránce můžete zobrazit následující informace:

- Pokud chcete zobrazit všechna předplatná, nabídky a delegování přidružená k zákazníkovi, vyberte jméno zákazníka.
- Pokud chcete zobrazit další podrobnosti o nabídce a jejích delegováních, vyberte název nabídky.
- Chcete-li zobrazit další podrobnosti o přiřazení rolí pro delegovaná předplatná nebo skupiny prostředků, vyberte položku ve sloupci **delegace** .

## <a name="view-and-manage-delegations"></a>Zobrazení a Správa delegování

Delegování zobrazuje předplatné nebo skupinu prostředků, které byly delegovány, spolu s uživateli a oprávněními, které k nim mají přístup. Chcete-li zobrazit tyto informace, vyberte možnost **delegace** na levé straně stránky **moji zákazníci** .

Možnosti v horní části stránky umožňují řadit, filtrovat a seskupovat tyto informace konkrétními zákazníky, nabídkami nebo klíčovými slovy.

### <a name="view-role-assignments"></a>Zobrazit přiřazení rolí

Uživatelé a oprávnění přidružená ke každému delegování se zobrazí ve sloupci **přiřazení rolí** . Jednotlivé položky můžete vybrat a zobrazit tak úplný seznam uživatelů, skupin a instančních objektů, kterým byl udělen přístup k předplatnému nebo skupině prostředků. Odtud můžete vybrat konkrétního uživatele, skupinu nebo hlavní název služby a získat další podrobnosti.

### <a name="remove-delegations"></a>Odebrat delegování

Pokud jste při připojování zákazníka ke službě Azure Lighthouse zahrnuli uživatele s [přiřazením k registraci spravovaných služeb](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) , můžou si uživatelé odebrat delegování tak, že vybere ikonu odpadkového koše, která se zobrazí na řádku tohoto delegování. V takovém případě nebudou mít žádní uživatelé v tenantovi poskytovatele služeb přístup k prostředkům, které byly dříve delegovány.

Další informace najdete v tématu [Odebrání přístupu k delegování](remove-delegation.md).

## <a name="view-delegation-change-activity"></a>Zobrazit aktivitu změny delegování

Část **Protokol aktivit** na stránce **moji Customers** sleduje pokaždé, když jsou předplatná zákazníků nebo skupiny prostředků delegovaná na vašeho tenanta, a pokaždé, když se odeberou dřív delegované prostředky. Tyto informace mohou zobrazit pouze uživatelé, kterým byla [přiřazena role čtenář monitorování v kořenovém oboru](monitor-delegation-changes.md).

Další informace najdete v tématu [zobrazení změn delegování v Azure Portal](monitor-delegation-changes.md#view-delegation-changes-in-the-azure-portal).

## <a name="work-in-the-context-of-a-delegated-subscription"></a>Práce v kontextu delegovaného předplatného

Můžete pracovat přímo v kontextu delegovaného předplatného v rámci Azure Portal bez přepínání adresáře, ke kterému jste se přihlásili. Postupujte následovně:

1. V horní části Azure Portal vyberte ikonu **adresář + předplatné** .
2. Ve **výchozím filtru předplatného** ověřte, že je vybrané jenom políčko pro toto delegované předplatné. Pomocí rozevíracího seznamu **aktuální + delegované adresáře** můžete zobrazit pouze odběry v rámci určitého adresáře. (Nepoužívejte možnost **adresáře Switch** , protože se změní adresář, ke kterému jste přihlášení.)

Pokud pak získáte přístup ke službě, která podporuje [prostředí pro správu mezi klienty](../concepts/cross-tenant-management-experience.md), služba bude ve výchozím nastavení kontextem delegovaného předplatného, které jste vybrali. To můžete změnit podle výše uvedených kroků a zaškrtnutím políčka **Vybrat vše** (nebo zvolením jednoho nebo více předplatných, která chcete místo toho použít).

> [!NOTE]
> Pokud jste vám udělili přístup k jedné nebo více skupinám prostředků místo přístupu k celému předplatnému, vyberte předplatné, ke kterému patří tato skupina prostředků. Pak budete pracovat v kontextu tohoto předplatného, ale budou mít přístup jenom k určeným skupinám prostředků.

K funkcím, které souvisí s delegovanými předplatnými nebo skupinami prostředků, se můžete dostat i v rámci služeb, které podporují prostředí pro správu klientů, a to výběrem předplatného nebo skupiny prostředků v rámci této služby.

## <a name="cloud-solution-provider-preview"></a>Poskytovatel řešení cloudu (Preview)

Oddíl samostatného **poskytovatele Cloud Solution Provider (Preview)** stránky **moji Customers** zobrazuje informace o fakturaci a prostředky pro zákazníky CSP, kteří [podepsali smlouvu o zákaznících Microsoftu (MCA)](/partner-center/confirm-customer-agreement) a jsou [v rámci plánu Azure](/partner-center/azure-plan-get-started). Další informace najdete v tématu [Začínáme s fakturačním účtem Microsoft Partner Agreement](../../cost-management-billing/understand/mpa-overview.md).

Tito zákazníci CSP se budou v této části zobrazovat bez ohledu na to, jestli je máte taky připojit do Azure Lighthouse. Podobně není nutné, aby se zákazník v části Customers **(Cloud Solution Provider)** ve svých **zákaznících** nemusel zobrazovat do Azure Lighthouse.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [prostředích pro správu mezi klienty](../concepts/cross-tenant-management-experience.md).
- Přečtěte si, jak můžou zákazníci [Zobrazit a spravovat poskytovatele služeb, a](view-manage-service-providers.md) to tak, že v Azure Portal přesměrují **poskytovatelé služeb** .

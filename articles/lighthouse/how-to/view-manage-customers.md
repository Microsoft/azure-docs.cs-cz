---
title: Zobrazení a Správa zákazníků a delegovaných prostředků
description: Jako poskytovatel služeb s využitím delegované správy prostředků Azure můžete zobrazit všechny svoje delegované prostředky a předplatná zákazníka tak, že na Azure Portal kliknete na moji zákazníci.
author: JnHs
ms.author: jenhayes
ms.service: lighthouse
ms.date: 10/23/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: d68ac0a1b6a66482a85f7ab79840099a34c731c2
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615542"
---
# <a name="view-and-manage-customers-and-delegated-resources"></a>Zobrazení a Správa zákazníků a delegovaných prostředků

Poskytovatelé služeb využívající [Azure delegovaných prostředků](../concepts/azure-delegated-resource-management.md) můžou pomocí stránky **moji zákazníci** v [Azure Portal](https://portal.azure.com) zobrazit delegované prostředky a odběry zákazníků. V takovém případě budeme odkazováni na poskytovatele služeb a zákazníky a podniky, které spravují víc tenantů, můžou stejný postup využít k konsolidaci prostředí pro správu.

Chcete-li získat přístup ke stránce **moji zákazníci** v Azure Portal, vyberte **všechny služby**a pak vyhledejte **Moje zákazníky** a vyberte ji. Můžete ji také najít zadáním "Moji zákazníci" do vyhledávacího pole v horní části Azure Portal.

Mějte na paměti, že horní část stránky **Customers** zobrazuje jenom informace o zákaznících, kteří mají delegované předplatné nebo skupiny prostředků. Pokud pracujete s ostatními zákazníky (například prostřednictvím [programu Cloud Solution Provider](https://docs.microsoft.com/partner-center/csp-overview), neuvidíte informace o těchto zákaznících, pokud nepřipojíte své prostředky k delegované správě prostředků. Na stránce níže v části **poskytovatel cloudového řešení (Preview)** se zobrazí informace o fakturaci a prostředky pro zákazníky CSP, kteří [podepsali smlouvu o zákaznících Microsoftu (MCA)](https://docs.microsoft.com/partner-center/confirm-customer-agreement) a jsou v rámci plánu Azure, ať už jsou nebo nejsou připojené. pro správu delegovaných prostředků Azure. Další informace najdete v tématu [Začínáme s fakturačním účtem Microsoft Partner Agreement](https://docs.microsoft.com/azure/billing/mpa-overview).

> [!NOTE]
> Vaši zákazníci mohou zobrazit informace o poskytovatelích služeb přechodem na **poskytovatele služeb** v Azure Portal. Další informace najdete v tématu [zobrazení a Správa poskytovatelů služeb](view-manage-service-providers.md).

## <a name="view-and-manage-customer-details"></a>Zobrazit a spravovat podrobnosti o zákazníkovi

Pokud chcete zobrazit podrobnosti o zákaznících, vyberte na levé straně stránky **Customers** možnost **zákazníci** .

Pro každého zákazníka uvidíte jméno zákazníka, ID zákazníka (ID tenanta) a nabídku přidruženou ke službě Engagement. Ve sloupci **delegování** se zobrazí počet delegovaných předplatných nebo počet delegovaných skupin prostředků.

> [!IMPORTANT]
> Aby bylo možné zobrazit delegování, uživatelé musí mít v procesu připojování udělenou roli [Čtenář](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader) (nebo jinou předdefinovanou roli, která zahrnuje přístup ke čtenářům).

Filtry v horní části stránky umožňují řadit a seskupovat informace o zákaznících nebo filtrovat podle konkrétních zákazníků, nabídek nebo klíčových slov.

Na této stránce můžete zobrazit následující informace:

- Pokud chcete zobrazit všechna předplatná, nabídky a delegování přidružená k zákazníkovi, vyberte jméno zákazníka.
- Pokud chcete zobrazit další podrobnosti o nabídce a jejích delegováních, vyberte název nabídky.
- Chcete-li zobrazit další podrobnosti o přiřazení rolí pro delegovaná předplatná nebo skupiny prostředků, vyberte položku ve sloupci **delegace** .

## <a name="view-delegations"></a>Zobrazit delegování

Delegování zobrazuje předplatné nebo skupinu prostředků, které byly delegovány, spolu s uživateli a oprávněními, které k nim mají přístup. Chcete-li zobrazit tyto informace, vyberte možnost **delegace** na levé straně stránky **moji zákazníci** .

Filtry v horní části stránky umožňují řadit a seskupovat informace o přiřazení přístupu nebo filtrovat podle konkrétních zákazníků, nabídek nebo klíčových slov.

Uživatelé a oprávnění přidružená ke každému delegování se zobrazí ve sloupci **přiřazení rolí** . Jednotlivé položky můžete vybrat a zobrazit tak úplný seznam uživatelů, skupin a instančních objektů, kterým byl udělen přístup k předplatnému nebo skupině prostředků. Odtud můžete vybrat konkrétního uživatele, skupinu nebo hlavní název služby a získat další podrobnosti.

## <a name="work-in-the-context-of-a-delegated-subscription"></a>Práce v kontextu delegovaného předplatného

V rámci Azure Portal můžete pracovat přímo v kontextu delegovaného předplatného, aniž byste museli přepínat adresář, ve kterém pracujete. Postupujte následovně:

1. V horní části Azure Portal vyberte ikonu **adresář + předplatné** .
2. V **globálním filtru předplatného** ověřte, že je vybrané jenom políčko pro toto delegované předplatné. Pomocí rozevíracího seznamu **aktuální + delegované adresáře** můžete zobrazit pouze odběry v rámci určitého adresáře. (Nepoužívejte možnost **adresáře Switch** , protože se změní adresář, ke kterému jste přihlášení.)

Pokud pak získáte přístup ke službě, která podporuje [prostředí pro správu mezi klienty](../concepts/cross-tenant-management-experience.md), služba bude ve výchozím nastavení kontextem delegovaného předplatného, které jste vybrali. To můžete změnit podle výše uvedených kroků a zaškrtnutím políčka **Vybrat vše** (nebo zvolením jednoho nebo více předplatných, která chcete místo toho použít).

> [!NOTE]
> Pokud jste vám udělili přístup k jedné nebo více skupinám prostředků místo přístupu k celému předplatnému, můžete vybrat předplatné, ke kterému patří tato skupina prostředků. Pak budete pracovat v kontextu tohoto předplatného, ale budou mít přístup jenom k určeným skupinám prostředků.

K funkcím, které souvisí s delegovanými předplatnými nebo skupinami prostředků, se můžete dostat i v rámci služeb, které podporují prostředí pro správu klientů, a to výběrem předplatného nebo skupiny prostředků v rámci této služby.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [prostředích pro správu mezi klienty](../concepts/cross-tenant-management-experience.md).
- Přečtěte si, jak můžou zákazníci [Zobrazit a spravovat poskytovatele služeb, a](view-manage-service-providers.md) to tak, že v Azure Portal přesměrují **poskytovatelé služeb** .

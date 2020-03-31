---
title: Zobrazení a správa zákazníků a delegovaných prostředků
description: Jako poskytovatel služeb pomocí azure delegované správy prostředků můžete zobrazit všechny delegované prostředky zákazníků a předplatná tak, že přejdete na můj zákazníkna portálu Azure.
ms.date: 01/22/2020
ms.topic: conceptual
ms.openlocfilehash: 0d4b3187066754e8a549f029623762df539b30b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76543422"
---
# <a name="view-and-manage-customers-and-delegated-resources"></a>Zobrazení a správa zákazníků a delegovaných prostředků

Poskytovatelé služeb, kteří používají [správu delegovaných prostředků Azure,](../concepts/azure-delegated-resource-management.md) můžou pomocí stránky **Moji zákazníci** na [webu Azure Portal](https://portal.azure.com) zobrazit delegované prostředky zákazníků a předplatná. I když budeme odkazovat na poskytovatele služeb a zákazníky zde, podniky spravující více klientů můžete použít stejný proces ke konsolidaci jejich prostředí pro správu.

Pokud chcete na webu Azure portal získat přístup na stránku **Moji zákazníci,** vyberte **Všechny služby**, **vyhledejte moji zákazníci** a vyberte je. Najdete ji také zadáním "Moji zákazníci" do vyhledávacího pole v horní části portálu Azure.

Nezapomeňte, že v části Hlavní **zákazníci** na stránce **Moji zákazníci** se zobrazují pouze informace o zákaznících, kteří delegovali předplatná nebo skupiny prostředků. Pokud spolupracujete s jinými zákazníky (například prostřednictvím [programu Zprostředkovatel cloudových řešení](https://docs.microsoft.com/partner-center/csp-overview)), nezobrazí se informace o těchto zákaznících v části **Zákazníci,** pokud jste nezadali jejich prostředky pro správu delegovaných prostředků Azure.

V samostatné části s názvem **Cloud Solution Provider (Preview)** se zobrazí fakturační údaje a prostředky pro zákazníky CSP, kteří [podepsali smlouvu se zákazníkem microsoftu (MCA)](https://docs.microsoft.com/partner-center/confirm-customer-agreement) a jsou [v rámci plánu Azure](https://docs.microsoft.com/partner-center/azure-plan-get-started). Další informace najdete [v tématu Začínáme s fakturačním účtem smlouvy Microsoft Partner .](../../billing/mpa-overview.md) Všimněte si, že tyto zákazníky CSP se zobrazí v této části bez ohledu na to, zda jste je také zadali pro správu delegovaných prostředků Azure. Podobně se zákazník zprostředkovatele csp nemusí zobrazit v části **Poskytovatel cloudových řešení (preview)** **Moji zákazníci,** abyste je mohli zavést pro správu delegovaných prostředků Azure.

> [!NOTE]
> Vaši zákazníci si můžou zobrazit informace o poskytovatelích služeb tak, že přejdete k **poskytovatelům služeb** na webu Azure Portal. Další informace najdete v tématu [Zobrazení a správa poskytovatelů služeb](view-manage-service-providers.md).

## <a name="view-and-manage-customer-details"></a>Zobrazení a správa podrobností o zákaznících

Chcete-li zobrazit podrobnosti o zákaznících, vyberte **možnost Zákazníci** na levé straně stránky **Moji zákazníci.**

U každého zákazníka uvidíte jméno zákazníka, ID zákazníka (ID klienta) a nabídku přidruženou k interakci. Ve sloupci **Delegování** uvidíte počet delegovaných předplatných nebo počet delegovaných skupin prostředků.

> [!IMPORTANT]
> Aby bylo možné zobrazit delegování, uživatelé musí mít udělena role [čtečky](../../role-based-access-control/built-in-roles.md#reader) (nebo jiné předdefinované role, která zahrnuje přístup čtenáře) v procesu registrace.

Filtry v horní části stránky umožňují seřadit a seskupit informace o zákaznících nebo filtrovat podle konkrétních zákazníků, nabídek nebo klíčových slov.

Na této stránce můžete zobrazit následující informace:

- Chcete-li zobrazit všechna předplatná, nabídky a delegace přidružené k zákazníkovi, vyberte jméno zákazníka.
- Chcete-li zobrazit další podrobnosti o nabídce a jejích delegacích, vyberte název nabídky.
- Chcete-li zobrazit další podrobnosti o přiřazení rolí pro delegované odběry nebo skupiny prostředků, vyberte položku ve **sloupci Delegování.**

## <a name="view-and-manage-delegations"></a>Zobrazení a správa delegací

Delegování zobrazit odběr nebo skupina prostředků, která byla delegována , spolu s uživateli a oprávnění, které mají přístup k němu. Chcete-li tyto informace zobrazit, vyberte **delegování** na levé straně stránky **Moji zákazníci.**

Filtry v horní části stránky umožňují seřadit a seskupit informace o přiřazení přístupu nebo filtrovat podle konkrétních zákazníků, nabídek nebo klíčových slov.

### <a name="view-role-assignments"></a>Zobrazit přiřazení rolí

Uživatelé a oprávnění přidružená ke každému delegování se zobrazí ve sloupci **Přiřazení rolí.** Můžete vybrat každou položku a zobrazit úplný seznam uživatelů, skupin a instančních objektů, kterým byl udělen přístup k předplatnému nebo skupině prostředků. Odtud můžete vybrat konkrétního uživatele, skupinu nebo hlavní název služby, abyste získali další podrobnosti.

### <a name="remove-delegations"></a>Odebrání delegací

Pokud jste zahrnuli uživatele s [rolí odstranění registrace spravovaných služeb](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) při registraci zákazníka pro správu delegovaných prostředků Azure, tito uživatelé mohou odebrat delegování výběrem ikony koše, která se zobrazí v řádku pro toto delegování. Pokud tak učiní, žádný uživatel v tenantovi poskytovatele služeb bude mít přístup k prostředkům, které byly dříve delegovány.


## <a name="work-in-the-context-of-a-delegated-subscription"></a>Práce v kontextu delegovaného předplatného

Můžete pracovat přímo v kontextu delegovaného předplatného v rámci portálu Azure, bez přepínání adresáře, ve kterém pracujete. Postupujte následovně:

1. V horní části portálu Azure vyberte ikonu **Adresář + předplatné.**
2. Ve filtru **globální předplatné,** ujistěte se, že je vybrána pouze pole pro toto delegované předplatné. Rozevírací seznam **Aktuální + delegované adresáře** můžete použít k zobrazení pouze předplatných v rámci určitého adresáře. (Nepoužívejte možnost **Přepnout adresář,** protože to změní adresář, ke kterému jste přihlášeni.)

Pokud pak získáte přístup ke službě, která podporuje [prostředí správy mezi klienty](../concepts/cross-tenant-management-experience.md), bude služba ve výchozím nastavení nastavena na kontext vybraného delegovaného předplatného. Můžete to změnit podle výše uvedených kroků a zaškrtnutím políčka **Vybrat vše** (nebo výběrem jednoho nebo více předplatných, ve kterých budete pracovat).

> [!NOTE]
> Pokud vám byl udělen přístup k jedné nebo více skupin prostředků, nikoli přístup k celé předplatné, můžete vybrat předplatné, do kterého patří tato skupina prostředků. Pak budete pracovat v kontextu tohoto předplatného, ale budete mít přístup pouze k určeným skupinám prostředků.

Můžete také získat přístup k funkcím souvisejícím s delegovanými předplatnými nebo skupinami prostředků v rámci služeb, které podporují prostředí správy mezi tenanty výběrem předplatného nebo skupiny prostředků z této služby.

## <a name="next-steps"></a>Další kroky

- Další informace o [prostředích správy mezi tenanty](../concepts/cross-tenant-management-experience.md).
- Zjistěte, jak můžou vaši zákazníci [zobrazit a spravovat poskytovatele služeb](view-manage-service-providers.md) tím, že přejdou na azure **portálu.**

---
title: Zobrazení a správa poskytovatelů služeb
description: Zákazníci můžou na stránce Poskytovatelé služeb na webu Azure Portal zobrazit informace o poskytovatelích služeb, nabídkách poskytovatelů služeb a delegovaných prostředcích.
ms.date: 02/25/2020
ms.topic: conceptual
ms.openlocfilehash: 94103c293ffa7ccfb9d7da0a237dc1b1c6540b72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270625"
---
# <a name="view-and-manage-service-providers"></a>Zobrazení a správa poskytovatelů služeb

Zákazníci můžou pomocí stránky **Poskytovatelé služeb** na [webu Azure Portal](https://portal.azure.com) zobrazit informace o poskytovatelích služeb a nabídkách poskytovatelů služeb, delegovat konkrétní prostředky prostřednictvím správy [delegovaných prostředků Azure](../concepts/azure-delegated-resource-management.md)a nakupovat nové nabídky poskytovatelů služeb. I když budeme odkazovat na poskytovatele služeb a zákazníky zde, podniky spravující více klientů můžete použít stejný proces ke konsolidaci jejich prostředí pro správu.

Chcete-li získat přístup na stránku **Poskytovatelé služeb** na webu Azure Portal, může zákazník vybrat **všechny služby**, vyhledat **poskytovatele služeb** a vybrat je. Můžou ho taky najít zadáním "Poskytovatelé služeb" do vyhledávacího pole v horní části portálu Azure.

Mějte na paměti, že stránka **Zprostředkovatelé služeb** zobrazuje pouze informace o poskytovatelích služeb, kteří mají přístup k předplatným zákazníka nebo skupinám prostředků prostřednictvím správy delegovaných prostředků Azure. Pokud zákazník spolupracuje s dalšími poskytovateli služeb, kteří nepoužívají správu delegovaných prostředků Azure pro přístup k prostředkům zákazníka, informace o těchto poskytovatelích služeb se zde nezobrazí.

> [!NOTE]
> Poskytovatelé služeb můžou zobrazit informace o svých zákaznících tak, že na webu Azure Portal přejdou na **Moji zákazníci.** Další informace najdete v tématu [Zobrazení a správa zákazníků a delegovaných prostředků](view-manage-customers.md).

## <a name="view-service-provider-details"></a>Zobrazit podrobnosti o poskytovateli služeb

Chcete-li zobrazit informace o poskytovatelích služeb, může zákazník vybrat **nabídky zprostředkovatelů** na levé straně stránky **Poskytovatelé služeb.**

Pro každou nabídku poskytovatele služeb zákazník uvidí jméno poskytovatele služeb a nabídku s ním spojenou spolu s názvem, který zákazník zadal během procesu registrace.

Ve sloupci **Delegování** zákazník vidí, kolik předplatných nebo skupin prostředků bylo delegováno na poskytovatele služeb pro tuto nabídku. Poskytovatel služeb bude mít přístup k těmto předplatným nebo skupinám prostředků a spravovat je podle úrovní přístupu uvedených v nabídce.

## <a name="delegate-resources"></a>Delegovat prostředky

Předtím, než může poskytovatel služeb přistupovat k prostředkům zákazníka a spravovat je, musí být delegován. Pokud zákazník nabídku přijal, ale dosud nedelegoval žádné prostředky, zobrazí se mu poznámka v horní části sekce **Nabídky poskytovatele.** To zákazníkovi umožní zjistit, že musí provést akci předtím, než bude mít poskytovatel služeb přístup k jakémukoli zdroji zákazníka.

Delegování předplatných nebo skupin prostředků:

1. Zaškrtněte políčko pro řádek obsahující poskytovatele služeb, nabídku a název. Pak v horní části obrazovky vyberte **Delegate resources.**
1. V části **Podrobnosti nabídky** na stránce **Prostředky delegáta** zkontrolujte podrobnosti o poskytovateli služeb a nabídce. Chcete-li zkontrolovat přiřazení rolí pro nabídku, vyberte **klepnutím sem zobrazíte podrobnosti o vybrané nabídce**.
1. V části **Delegát** vyberte **Delegate subscriptions** nebo **Delegate resource groups**.
1. Vyberte předplatná nebo skupiny prostředků, které chcete delegovat pro tuto nabídku, a pak vyberte **Přidat**.
1. Zaškrtnutím políčka v dolní části stránky potvrďte, že chcete tomuto poskytovateli služeb udělit přístup k vybraným prostředkům, a pak vyberte **Delegát**.

## <a name="add-or-remove-service-provider-offers"></a>Přidání nebo odebrání nabídek poskytovatelů služeb

Zákazník může přidat novou nabídku poskytovatele služeb ze stránky **Nabídky poskytovatele** výběrem **možnosti Přidat nabídku**. Poskytovatel služeb musí zveřejnit nabídku pro tohoto zákazníka. Zákazník pak může vybrat tuto nabídku z obrazovky **Soukromé nabídky** a pak vybrat **Vytvořit**.

Pokud chce zákazník odebrat nabídku poskytovatele služeb, může vybrat ikonu koše v řádku pro tuto nabídku. Po potvrzení odstranění již tento poskytovatel služeb nebude mít přístup k prostředkům zákazníků, které byly dříve delegovány pro tuto nabídku.

## <a name="update-service-provider-offers"></a>Aktualizace nabídek poskytovatelů služeb

Po přidání nabídky zákazníkmůže poskytovatel služeb publikovat aktualizovanou verzi stejné nabídky na Azure Marketplace. Mohou například chtít přidat novou definici role. Pokud byla zveřejněna nová verze nabídky, zobrazí se na stránce **Nabídky Poskytovatele** v řádku pro tuto nabídku ikona "aktualizace". Zákazník může vybrat tuto ikonu a zobrazit rozdíly mezi aktuální verzí nabídky a novou verzí.

 ![Ikona nabídky aktualizace](../media/update-offer.jpg)

Po přezkoumání změn se zákazník může rozhodnout aktualizovat na novou verzi. Jakmile tak učiní, autorizace a další nastavení zadaná v nové verzi se budou vztahovat na všechna předplatná nebo skupiny prostředků, které byly pro danou nabídku delegovány.

## <a name="view-delegations"></a>Zobrazit delegace

Delegování představují přiřazení rolí, která udělují oprávnění poskytovateli služeb pro prostředky, které zákazník delegoval. Chcete-li tyto informace zobrazit, vyberte **delegování** na levé straně stránky **Poskytovatelé služeb.**

Filtry v horní části stránky umožňují seřadit a seskupit informace o delegaci. Můžete také filtrovat podle konkrétních zákazníků, nabídek nebo klíčových slov.

> [!NOTE]
> Zákazníci neuvidí tato přiřazení rolí ani žádné uživatele z tenanta poskytovatele služeb, kterým byly tyto role uděleny, při [zobrazení informací o přiřazení role pro delegovaný obor na portálu Azure](../../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-at-a-scope) nebo prostřednictvím rozhraní API.

## <a name="audit-delegations-in-your-environment"></a>Auditní delegování ve vašem prostředí

Zákazníci mohou chtít získat přehled o předplatných nebo skupinách prostředků, které byly delegovány na poskytovatele služeb pro [správu delegovaných prostředků Azure](../concepts/azure-delegated-resource-management.md). To je užitečné zejména pro zákazníky s velkým počtem předplatných nebo kteří mají mnoho uživatelů, kteří provádějí úlohy správy.

Poskytujeme [předdefinovanou definici zásad Azure](../../governance/policy/samples/built-in-policies.md#lighthouse) pro auditování delegování oborů na spravovaného klienta. Tuto zásadu můžete přiřadit skupině pro správu, která obsahuje všechna předplatná, která chcete auditovat. Při kontrole dodržování těchto zásad budou všechna delegovaná předplatná nebo skupiny prostředků (v rámci skupiny pro správu, ke které je zásada přiřazena) zobrazena ve stavu nedodržující předpisy. Poté můžete zkontrolovat výsledky a potvrdit, že neexistují žádné neočekávané delegace.

Další informace o tom, jak přiřadit zásadu a zobrazit výsledky stavu dodržování předpisů, najdete v [tématu Úvodní příručka: Vytvoření přiřazení zásad](../../governance/policy/assign-policy-portal.md).

## <a name="next-steps"></a>Další kroky
 
- Další informace o [Azure Lighthouse](../overview.md).
- Zjistěte, jak můžou poskytovatelé služeb [zobrazit a spravovat zákazníky](view-manage-customers.md) tak, že přejdou na můj **zákazník** na webu Azure Portal.
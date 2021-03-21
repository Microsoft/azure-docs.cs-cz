---
title: Role a oprávnění pro službu Azure Data Factory
description: Popisuje role a oprávnění potřebná k vytváření datových továren a práci s podřízenými prostředky.
ms.date: 11/5/2018
ms.topic: conceptual
ms.service: data-factory
author: dcstwh
ms.author: weetok
ms.openlocfilehash: cec5df9a5046e912ab8542c91bde4344affa0925
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100364473"
---
# <a name="roles-and-permissions-for-azure-data-factory"></a>Role a oprávnění pro službu Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]


Tento článek popisuje role potřebné k vytváření a správě Azure Data Factorych prostředků a oprávnění udělených těmito rolemi.

## <a name="roles-and-requirements"></a>Role a požadavky

Pokud chcete vytvořit Data Factory instance, uživatelský účet, který použijete pro přihlášení k Azure, musí být členem role *Přispěvatel* , role *vlastníka* nebo *správce* předplatného Azure. Pokud chcete zobrazit oprávnění, která máte v předplatném, na webu Azure Portal v pravém horním rohu vyberte své uživatelské jméno a potom **Oprávnění**. Pokud máte přístup k několika předplatným, vyberte odpovídající předplatné. 

Při vytváření a správě podřízených prostředků pro službu Data Factory, včetně datových sad, propojených služeb, kanálů, triggerů a prostředí Integration Runtime, platí následující požadavky:
- K vytváření a správě podřízených prostředků v Azure Portal musíte patřit do role **přispěvatel Data Factory** na úrovni **skupiny prostředků** nebo výše.
- Pro vytváření a správu podřízených prostředků pomocí PowerShellu nebo sady SDK na úrovni prostředku nebo vyšší je dostatečná role **Přispěvatel**.

Ukázku pokynů pro přidání uživatele do role najdete v článku věnovaném [přidávání rolí](../cost-management-billing/manage/add-change-subscription-administrator.md).

## <a name="set-up-permissions"></a>Nastavení oprávnění

Po vytvoření Data Factory možná budete chtít umožnit ostatním uživatelům pracovat s datovou továrnou. Pokud chcete tento přístup ostatním uživatelům udělit, musíte je přidat do předdefinované role **přispěvatele Data Factory** ve **skupině prostředků** , která obsahuje data Factory.

### <a name="scope-of-the-data-factory-contributor-role"></a>Rozsah role Přispěvatel Data Factory

Členství v roli **přispěvatel Data Factory** umožňuje uživatelům provádět následující akce:
- Vytvářejte, upravujte a odstraňujte datové továrny a podřízené prostředky, včetně datových sad, propojených služeb, kanálů, triggerů a prostředí Integration runtime.
- Nasaďte šablony Správce prostředků. Nasazení Správce prostředků je metoda nasazení, kterou používá Data Factory v Azure Portal.
- Spravujte výstrahy App Insights pro datovou továrnu.
- Vytvořte lístky podpory.

Další informace o této roli najdete v tématu [Data Factory role přispěvatele](../role-based-access-control/built-in-roles.md#data-factory-contributor).

### <a name="resource-manager-template-deployment"></a>Nasazení šablony Správce prostředků

Role **přispěvatel Data Factory** na úrovni skupiny prostředků nebo výše umožňuje uživatelům nasadit správce prostředků šablony. V důsledku toho mohou členové role použít šablony Správce prostředků k nasazení datových továren a jejich podřízených prostředků, včetně datových sad, propojených služeb, kanálů, triggerů a prostředí Integration runtime. Členství v této roli neumožňuje uživateli vytvářet další prostředky.

Oprávnění pro Azure Repos a GitHub jsou nezávislá na oprávněních Data Factory. V důsledku toho uživatel s oprávněními úložiště, který je členem role čtenář, může upravovat Data Factory podřízených prostředků a potvrdit změny v úložišti, ale nemůže tyto změny publikovat.


> [!IMPORTANT]
> Nasazení šablon Správce prostředků s rolí **přispěvatele Data Factory** nezvyšuje vaše oprávnění. Pokud například nasadíte šablonu, která vytvoří virtuální počítač Azure, a nemáte oprávnění k vytváření virtuálních počítačů, nasazení se nepovede a dojde k chybě autorizace.

   V kontextu publikování se vztahují oprávnění **Microsoft. DataFactory/Factory/Write** na následující režimy.
- Toto oprávnění je vyžadováno v živém režimu pouze v případě, že zákazník změní globální parametry.
- Toto oprávnění je vždy vyžadováno v režimu Git, protože pokaždé, když se zákazník publikuje, je potřeba aktualizovat objekt factory s posledním ID potvrzení.

### <a name="custom-scenarios-and-custom-roles"></a>Vlastní scénáře a vlastní role

Někdy může být nutné udělit různým uživatelům služby Data Factory různé úrovně přístupu. Například:
- Možná budete potřebovat skupinu, kde uživatelé mají oprávnění pouze pro konkrétní objekt pro vytváření dat.
- Nebo možná budete potřebovat skupinu, kde uživatelé můžou monitorovat jenom objekt pro vytváření dat (nebo továrny), ale nemůžou ho upravovat.

Tyto vlastní scénáře můžete dosáhnout vytvořením vlastních rolí a přiřazením uživatelů k těmto rolím. Další informace o vlastních rolích najdete v tématu [vlastní role v Azure](..//role-based-access-control/custom-roles.md).

Tady je několik příkladů, které ukazují, co můžete dosáhnout s vlastními rolemi:

- Umožní uživateli vytvořit, upravit nebo odstranit jakoukoli datovou továrnu ve skupině prostředků z Azure Portal.

  Přiřaďte předdefinované role **přispěvatele Data Factory** na úrovni skupiny prostředků pro uživatele. Pokud chcete přístup k libovolné datové továrně v rámci předplatného zpřístupnit, přiřaďte roli na úrovni předplatného.

- Umožní uživateli zobrazení (číst) a monitorovat objekt pro vytváření dat, ale nemůže ho upravovat ani měnit.

  Přiřaďte k prostředku služby Data Factory pro uživatele integrovanou roli **Čtenář** .

- Umožní uživateli upravovat v Azure Portal jedinou datovou továrnu.

  Tento scénář vyžaduje dvě přiřazení rolí.

  1. Přiřaďte integrovanou roli **přispěvatele** na úrovni datové továrny.
  2. Vytvořte vlastní roli s oprávněním  **Microsoft. Resources/Deployments/**. Přiřaďte tuto vlastní roli uživateli na úrovni skupiny prostředků.

- Umožní uživateli otestovat připojení v propojené službě nebo zobrazit data v datové sadě.

    Vytvořte vlastní roli s oprávněními pro následující akce: **Microsoft. DataFactory/Factory/getFeatureValue/Read** a **Microsoft. DataFactory/Factory/getDataPlaneAccess/Action**. Přiřaďte tuto vlastní roli k prostředku datové továrny pro uživatele.

- Umožněte uživateli aktualizovat datovou továrnu z PowerShellu nebo sady SDK, ale ne v Azure Portal.

  Přiřaďte k prostředku datové továrny integrovanou roli **přispěvatele** pro uživatele. Tato role uživateli umožňuje zobrazit prostředky v Azure Portal, ale uživatel nemá přístup k tlačítkům  **publikovat** a **Publikovat všechna** .


## <a name="next-steps"></a>Další kroky

- Další informace o rolích v Azure – [pochopení definic rolí](../role-based-access-control/role-definitions.md)

- Přečtěte si další informace o [roli Přispěvatel](../role-based-access-control/built-in-roles.md#data-factory-contributor)rolí přispěvatel **Data Factory** Data Factory.

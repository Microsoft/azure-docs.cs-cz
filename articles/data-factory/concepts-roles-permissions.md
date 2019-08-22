---
title: Role a oprávnění pro Azure Data Factory | Microsoft Docs
description: Popisuje role a oprávnění potřebná k vytváření datových továren a práci s podřízenými prostředky.
ms.date: 11/5/2018
ms.topic: conceptual
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
ms.tgt_pltfrm: na
author: gauravmalhot
ms.author: gamal
manager: craigg
ms.openlocfilehash: 765464f8593e217fba0b564a1fabad7777e94a36
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2019
ms.locfileid: "69873597"
---
# <a name="roles-and-permissions-for-azure-data-factory"></a>Role a oprávnění pro Azure Data Factory

Tento článek popisuje role potřebné k vytváření a správě Azure Data Factorych prostředků a oprávnění udělených těmito rolemi.

## <a name="roles-and-requirements"></a>Role a požadavky

Pro vytvoření instancí služby Data Factory musí být uživatelský účet, který použijete pro přihlášení k Azure, členem role *přispěvatel* nebo *vlastník* nebo *správcem* předplatného Azure. Pokud chcete zobrazit oprávnění, která máte v předplatném, na webu Azure Portal v pravém horním rohu vyberte své uživatelské jméno a potom **Oprávnění**. Pokud máte přístup k několika předplatným, vyberte odpovídající předplatné. 

Při vytváření a správě podřízených prostředků pro službu Data Factory, včetně datových sad, propojených služeb, kanálů, triggerů a prostředí Integration Runtime, platí následující požadavky:
- Pokud chcete vytvářet a spravovat podřízené prostředky na webu Azure Portal, je potřeba, abyste patřili do role **Přispěvatel Data Factory** na úrovni skupiny prostředků nebo vyšší.
- Pro vytváření a správu podřízených prostředků pomocí PowerShellu nebo sady SDK na úrovni prostředku nebo vyšší je dostatečná role **Přispěvatel**.

Ukázku pokynů pro přidání uživatele do role najdete v článku věnovaném [přidávání rolí](../billing/billing-add-change-azure-subscription-administrator.md).

## <a name="set-up-permissions"></a>Nastavení oprávnění

Po vytvoření Data Factory možná budete chtít umožnit ostatním uživatelům pracovat s datovou továrnou. Pokud chcete tento přístup ostatním uživatelům udělit, musíte je přidat do předdefinované role **přispěvatele Data Factory** ve skupině prostředků, která obsahuje datovou továrnu.

### <a name="scope-of-the-data-factory-contributor-role"></a>Rozsah role Přispěvatel Data Factory

Členství v roli **přispěvatel Data Factory** umožňuje uživatelům provádět následující akce:
- Vytvářejte, upravujte a odstraňujte datové továrny a podřízené prostředky, včetně datových sad, propojených služeb, kanálů, triggerů a prostředí Integration runtime.
- Nasaďte šablony Správce prostředků. Nasazení Správce prostředků je metoda nasazení, kterou používá Data Factory v Azure Portal.
- Spravujte výstrahy App Insights pro datovou továrnu.
- Vytvořte lístky podpory.

Další informace o této roli najdete v tématu [Data Factory role přispěvatele](../role-based-access-control/built-in-roles.md#data-factory-contributor).

### <a name="resource-manager-template-deployment"></a>Nasazení šablony Resource Manageru

Role **přispěvatel Data Factory** na úrovni skupiny prostředků nebo výše umožňuje uživatelům nasadit správce prostředků šablony. V důsledku toho mohou členové role použít šablony Správce prostředků k nasazení datových továren a jejich podřízených prostředků, včetně datových sad, propojených služeb, kanálů, triggerů a prostředí Integration runtime. Členství v této roli ale neumožňuje uživateli vytvářet další prostředky.

Oprávnění pro Azure Repos a GitHub jsou nezávislá na oprávněních Data Factory. V důsledku toho uživatel s oprávněními úložiště, který je členem role čtenář, může upravovat Data Factory podřízených prostředků a potvrdit změny v úložišti, ale nemůže tyto změny publikovat.

> [!IMPORTANT]
> Nasazení šablon Správce prostředků s rolí **přispěvatele Data Factory** nezvyšuje vaše oprávnění. Pokud například nasadíte šablonu, která vytvoří virtuální počítač Azure, a nemáte oprávnění k vytváření virtuálních počítačů, nasazení se nepovede a dojde k chybě autorizace.

### <a name="custom-scenarios-and-custom-roles"></a>Vlastní scénáře a vlastní role

Někdy může být nutné udělit různým uživatelům služby Data Factory různé úrovně přístupu. Příklad:
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
  2. Vytvořte vlastní roli s oprávněním **Microsoft. Resources/Deployments/** . Přiřaďte tuto vlastní roli uživateli na úrovni skupiny prostředků.

- Dovolit uživateli, aby mohl testovat připojení pouze v propojené službě

    Vytvořte vlastní roli role s oprávněními pro následující akce: **Microsoft. DataFactory/Factory/getFeatureValue/Read** a **Microsoft. DataFactory/Factory/getDataPlaneAccess/Read**. Přiřaďte tuto vlastní roli k prostředku datové továrny pro uživatele.

- Umožněte uživateli aktualizovat datovou továrnu z PowerShellu nebo sady SDK, ale ne v Azure Portal.

  Přiřaďte k prostředku datové továrny integrovanou roli přispěvatele pro uživatele. Tato role uživateli umožňuje zobrazit prostředky v Azure Portal, ale uživatel nemá přístup k tlačítkům **publikovat** a **Publikovat všechna** .

## <a name="next-steps"></a>Další postup

- Další informace o rolích v Azure – [pochopení definic rolí](../role-based-access-control/role-definitions.md)

- Přečtěte si další informace o [roli Přispěvatel](../role-based-access-control/built-in-roles.md#data-factory-contributor)rolí přispěvatel **Data Factory** Data Factory.

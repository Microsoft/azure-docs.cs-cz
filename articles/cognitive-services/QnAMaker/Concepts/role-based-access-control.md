---
title: Spolupráce s ostatními QnA Maker
description: Naučte se spolupracovat s ostatními autory a editory pomocí řízení přístupu na základě role v Azure.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 05/15/2020
ms.openlocfilehash: c411250f7d850e56c9c54c27e888cba8f3acfece
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2021
ms.locfileid: "100522202"
---
# <a name="collaborate-with-other-authors-and-editors"></a>Spolupráce s ostatními autory a editory

Spolupracujte s ostatními autory a editory pomocí řízení přístupu na základě role Azure (Azure RBAC), které jsou umístěné na vašem prostředku QnA Maker.

## <a name="access-is-provided-on-the-qna-maker-resource"></a>Přístup je k dispozici na prostředku QnA Maker

Všechna oprávnění jsou řízená oprávněními, která jsou umístěna v prostředku QnA Maker. Tato oprávnění se zarovnají ke čtení, zápisu, publikování a úplnému přístupu.

Tato funkce Azure RBAC zahrnuje:
* Azure Active Directory (AAD) je 100% zpětně kompatibilní s ověřováním pomocí klíčů pro vlastníky a přispěvatele. Zákazníci můžou ve svých žádostech použít ověřování založené na klíčích nebo ověřování na základě služby Azure RBAC.
* Rychle přidejte autory a editory do všech databází znalostí v prostředku, protože je řízení na úrovni prostředků, nikoli na úrovni znalostní báze.

> [!NOTE]
> Nezapomeňte do prostředku přidat vlastní subdoménu. [Vlastní subdoména](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains) by měla být ve výchozím nastavení přítomná, ale pokud ne, přidejte ji prosím.

## <a name="access-is-provided-by-a-defined-role"></a>Přístup poskytuje definovaná role.

[!INCLUDE [Azure RBAC permissions table](../includes/role-based-access-control.md)]

## <a name="authentication-flow"></a>Tok ověřování

Následující diagram znázorňuje tok z pohledu autora pro přihlášení k portálu QnA Maker a použití rozhraní API pro vytváření obsahu.

> [!div class="mx-imgBorder"]
> ![Následující diagram znázorňuje tok z pohledu autora pro přihlášení k portálu QnA Maker a použití rozhraní API pro vytváření obsahu.](../media/qnamaker-how-to-collaborate-knowledge-base/rbac-flow-from-portal-to-service.png)

|Postup|Popis|
|--|--|
|1|Portál získá token pro QnA Maker prostředek.|
|2|Portál volá příslušné rozhraní API pro vytváření QnA Maker (APIM), které předá token místo klíčů.|
|3|Rozhraní API služby QnA Maker ověří token.|
|4 |Rozhraní API služby QnA Maker volá službu Qnamakerem.|

Pokud máte v úmyslu volat [vytváření rozhraní API](../index.yml), přečtěte si další informace o nastavení ověřování.

## <a name="authenticate-by-qna-maker-portal"></a>Ověřování pomocí portálu QnA Maker

Pokud vytváříte a spolupracujete s portálem QnA Maker, po přidání příslušné role k prostředku pro spolupracovníka QnA Maker portál spravuje všechna přístupová oprávnění.

## <a name="authenticate-by-qna-maker-apis-and-sdks"></a>Ověřování pomocí QnA Maker rozhraní API a sad SDK

Pokud vytváříte a spolupracujete s rozhraními API, a to buď prostřednictvím REST nebo sad SDK, musíte [vytvořit instanční objekt](../../authentication.md#assign-a-role-to-a-service-principal) pro správu ověřování.

## <a name="next-step"></a>Další krok

* Návrh znalostní báze pro jazyky a pro klientské aplikace

---
title: Přihlašovací údaje v Azure obsahu moderátora | Microsoft Docs
description: Správa obsahu moderátora pověření pro použití s rozhraní API.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 06/25/2017
ms.author: sajagtap
ms.openlocfilehash: 4531fa4c8bbb7bb9c1daeaaac6f9e7078dae250a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342410"
---
# <a name="manage-credentials"></a>Správa přihlašovacích údajů

Vaše přihlašovací údaje obsahu moderátora vytvářejí v následujících umístěních:

- [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator)
- [Zkontrolujte nástroj obsahu moderátora](http://contentmoderator.cognitive.microsoft.com/)

Tento článek vysvětluje, kde je najít a jak se vztahují k sobě navzájem.

## <a name="the-azure-portal"></a>Portál Azure

Na řídicím panelu Azure portálu vyberte svůj účet moderátora obsahu. V části **Správa prostředků**, vyberte **klíče**. Pokud chcete zkopírovat klíč, vyberte ikonu napravo od klíč.

![Obsahu moderátora klíče na portálu Azure](images/credentials-azure-portal-keys.PNG)

### <a name="how-to-use-your-azure-account-with-the-review-tool"></a>Postup použití účtu Azure pomocí nástroje Kontrola
Kontrola rozhraní API používat klíč Azure, zkopírujte ID prostředku, který je uvedený na **vlastnosti** obrazovky na následujícím snímku obrazovky a zadejte na obrazovce nástroj zkontrolujte přihlašovací údaje **ID prostředku seznam povolených adres** polí, jak je znázorněno v následujícím **ID prostředku** části. 

Pokud chcete použít klíč Azure k dispozici v rámci obsahu moderátora pracovních postupů, zadejte ho v **Ocp-Apim-Subscription-Key** pole **nastavení pracovních postupů** části, jak je znázorněno v následujícím  **Pracovní postupy** části.

> [!NOTE]
> Jakmile je nahradit dvou místech ve nástroj Zkontrolujte klíč a ID prostředku ze svého předplatného Azure vaše **zkušební verze Ocp-Apim-Subscription-Key** zobrazí na přihlašovací údaje obrazovky se už nepoužívá, ale je vždy k dispozici.
> Zkušební klíč omezeny pouze na maximální 5 000 transakce měsíčně v požadavků za sekundu (RPS) na 1.

![ID obsahu moderátora prostředků na portálu Azure](images/credentials-azure-portal-resourceid.PNG)


## <a name="the-review-tool"></a>Zkontrolujte nástroj

Na kontrola nástroje na řídicí panel, **nastavení** vyberte **přihlašovací údaje**.

![Obsahu moderátora přihlašovacích údajů v nástroji Kontrola](images/credentials-trial-resource-workflow.PNG)

V následující části prozkoumá předchozí obrázek podrobněji:


### <a name="api"></a>Rozhraní API

První část seznamy vaše **zkontrolujte koncový bod rozhraní API**, **team ID**a **Ocp-Apim-Subscription-Key (klíč obsahu moderátora zkušební verze)** generované v rámci týmu revize vytvoření. Je použijte k volání všechny obsahu moderátora rozhraní API, včetně rozhraní API revize.

Všimněte si také identifikátor vaší oblasti pro svůj koncový bod rozhraní API. Například **westus** oblast v "https://westus.api.cognitive.microsoft.com/contentmoderator/review/v1.0"

![Klíč obsahu moderátora v nástroji Kontrola](images/credentials-trialkey.PNG)


### <a name="resource-id"></a>ID prostředku

Druhá část spustí se jako prázdný s žádné ID prostředku. **Na používání vašeho předplatného Azure klíče s rozhraním API kontrolní, přejděte na obrazovce ID prostředků, jak je znázorněno dříve a zkopírujte jej do pole ukazuje**. Stiskněte tlačítko **'+'** uložit vaše ID prostředku.

> [!NOTE]
> Vaše předplatné obsahu moderátora oblast by měl odpovídat týmem zkontrolujte oblast pro něj rozpoznat váš tým a přístup k datům týmu. Například v bitové kopie na této stránce **západní USA** oblast **(4)** obsahuje předplatné Azure obsahu moderátora a váš tým revize.

![ID obsahu moderátora prostředků v nástroji Kontrola](images/credentials-resourceids.PNG)


### <a name="workflows"></a>Pracovní postupy

Třetí část obsahuje informace, používá pro spouštění pracovních postupů. Spustí se zobrazuje ve výchozím nastavení automaticky vygenerované zkušební klíč. 

**Jej aktualizovat s klíč Azure při získání předplatného Azure**. Tato dvě pole Povolit použití seznamů termín a bitové kopie v operacích obrazovky textových a obrázkových vyhodnotit v uvedeném pořadí.

![Obsahu přihlašovací údaje moderátora pracovního postupu v nástroji Kontrola](images/credentials-workflow.PNG)


## <a name="next-steps"></a>Další postup

* Další informace o použití obsahu moderátora přihlašovací údaje v vaše [pracovních](workflows.md).

---
title: Přihlašovací údaje v Azure Content Moderator | Dokumentace Microsoftu
description: Správa přihlašovacích údajů Content Moderator pro použití s rozhraním API.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 06/25/2017
ms.author: sajagtap
ms.openlocfilehash: 6477879953dc2bb2c7503eb0b2d4b5effa7b6a11
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2018
ms.locfileid: "44024651"
---
# <a name="manage-credentials"></a>Správa přihlašovacích údajů

Vaše přihlašovací údaje Content Moderatoru se vytvoří v následujících umístěních:

- [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator)
- [Nástroj pro recenze Content Moderatoru](http://contentmoderator.cognitive.microsoft.com/)

Tento článek popisuje, kde je najít a jejich vzájemné vztahy mezi sebou.

## <a name="the-azure-portal"></a>Azure Portal

Na řídicím panelu Azure portal vyberte svůj účet Content Moderatoru. V části **správy prostředků**vyberte **klíče**. Zkopírování klíče, výběrem ikony napravo od klíč.

![Content Moderator klíče na webu Azure Portal](images/credentials-azure-portal-keys.PNG)

### <a name="use-the-azure-account-with-the-review-tool-and-review-api"></a>Účet Azure pomocí nástroje pro recenze a projděte si rozhraní API
K použití příslušného klíče Azure pomocí rozhraní API pro kontrolu, zkopírujte ID prostředku na **vlastnosti** obrazovky na následujícím snímku obrazovky a zadejte ho na obrazovce přihlašovacích údajů pro nástroj pro recenze v **ID prostředků na seznamu povolených** pole, jak je znázorněno v následujícím **ID prostředku** oddílu. 

> [!NOTE]
> Vaše předplatné Content Moderator oblasti by měl odpovídat oblasti tým kontroly, aby se rozpoznat váš tým a přístup k datům týmu. Například na obrázcích na této stránce **USA – západ** oblasti **(4)** obsahuje předplatné Content Moderator Azure a váš tým kontroly.
>
> Až nahradíte dvě místa v nástroj pro recenze klíč a ID prostředku z vašeho předplatného Azure, vaše **zkušební verze Ocp-Apim-Subscription-Key** zobrazuje na přihlašovací údaje obrazovky se už nepoužívá, ale je vždy k dispozici.
> Klíče zkušební verze, jste omezeni maximální 5000 transakcí za měsíc na 1 žádost za sekundu (předávajících stran).

![Content Moderator ID prostředku na webu Azure Portal](images/credentials-azure-portal-resourceid.PNG)

### <a name="use-the-azure-account-with-the-workflows-in-the-review-tool"></a>Použít účet Azure s pracovními postupy v nástroj pro recenze

K použití příslušného klíče Azure k dispozici v rámci Content Moderator pracovních postupů, zadejte ho v **Ocp-Apim-Subscription-Key** pole **nastavení pracovního postupu** jak je znázorněno v následující části  **Pracovní postupy** oddílu. Klikněte **'+'** uložit vaše ID prostředku.

![Content Moderator přihlašovacích údajů pracovního postupu v nástroj pro recenze](images/credentials-workflow.PNG)

## <a name="the-review-tool"></a>Nástroj pro recenze

K revizi nástroj na řídicí panel, **nastavení** kartu, vyberte možnost **pověření**.

![Content Moderator přihlašovací údaje v nástroj pro recenze](images/credentials-trial-resource-workflow.PNG)

Následující oddíl se zabývá podrobněji na předchozím obrázku:

### <a name="api"></a>Rozhraní API

V první části jsou uvedeny vaší **zkontrolujte koncový bod rozhraní API**, **ID tenanta**a **Ocp-Apim-Subscription-Key (Content Moderator zkušební klíč)** vygenerované v rámci vašeho týmu na revizi vytvoření. Je použijte k volání všechna rozhraní API Content Moderatoru, včetně rozhraní API pro kontrolu.

Všimněte si také identifikátor vaší oblasti pro váš koncový bod rozhraní API. Například **westus** je oblast v "https://westus.api.cognitive.microsoft.com/contentmoderator/review/v1.0"

![Content Moderator klíče v nástroj pro recenze](images/credentials-trialkey.PNG)

### <a name="resource-id"></a>ID prostředku

Jsme probrali v této části [pomocí svého účtu Azure pomocí nástroje pro recenze a rozhraní API](credentials.md#how-to-use-your-azure-account-with-the-review-tool) oddílu. Toto pole je obvykle prázdné, pokud vaše Id prostředku Azure přidáte k tomuto poli jak je popsáno v předchozí části.

### <a name="workflows"></a>Pracovní postupy

Tuto sadu polí v předchozí části jsme probrali v [spouštění pracovních postupů pomocí Azure key](credentials.md#use-the-azure-account-with-the-workflows-in-the-review-tool). Ve výchozím nastavení nástroj pro recenze pomocí klíče zkušební verze automaticky generované pro spouštění pracovních postupů a, který se zobrazuje na začátku. Dvě pole umožňují, které používají seznamy termínů a bitové kopie v operacích obrazovky textových a obrázkových vyhodnotit v uvedeném pořadí.

## <a name="next-steps"></a>Další postup

* Další informace o použití Content Moderator přihlašovacím údajům vašeho [pracovních postupů](workflows.md).

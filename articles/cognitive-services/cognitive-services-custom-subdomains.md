---
title: Vlastní subdomény
titleSuffix: Azure Cognitive Services
description: Názvy vlastních subdomén pro každý prostředek služby vydaných služeb se vytvářejí prostřednictvím Azure Portal, Azure Cloud Shell nebo rozhraní příkazového řádku Azure.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: erhopf
ms.openlocfilehash: 95f3de0fe26678f6b4bcd97a78c06e92b72a7135
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2020
ms.locfileid: "94368810"
---
# <a name="custom-subdomain-names-for-cognitive-services"></a>Názvy vlastních subdomén pro Cognitive Services

Azure Cognitive Services pro každý prostředek vytvořený prostřednictvím [Azure Portal](https://portal.azure.com), [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/)nebo rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli)používá vlastní názvy subdomén. Na rozdíl od regionálních koncových bodů, které byly společné pro všechny zákazníky v konkrétní oblasti Azure, jsou názvy vlastních subdomén pro daný prostředek jedinečné. Pro povolení funkcí, jako je například Azure Active Directory (Azure AD) pro ověřování, jsou vyžadovány názvy vlastních subdomén.

## <a name="how-does-this-impact-existing-resources"></a>Jak to ovlivní stávající prostředky?

Cognitive Services prostředky vytvořené před 1. července 2019 použijí místní koncové body pro přidruženou službu. Tyto koncové body budou fungovat se stávajícími a novými prostředky.

Pokud chcete migrovat existující prostředek, abyste mohli využívat názvy vlastních subdomén, abyste mohli povolit funkce jako Azure AD, postupujte podle těchto pokynů:

1. Přihlaste se k Azure Portal a vyhledejte prostředek Cognitive Services, do kterého chcete přidat vlastní název subdomény.
2. V okně **Přehled** vyhledejte a vyberte **generovat vlastní název domény**.
3. Otevře se panel s pokyny pro vytvoření jedinečné vlastní subdomény pro váš prostředek.
   > [!WARNING]
   > Po vytvoření vlastní subdomény název **nelze** změnit.

## <a name="do-i-need-to-update-my-existing-resources"></a>Potřebuji aktualizovat moje stávající prostředky?

Ne. Místní koncový bod bude fungovat i nadále pro nové a stávající Cognitive Services a název vlastní subdomény je nepovinný. I když se přidá název vlastní subdomény, místní koncový bod bude i nadále pracovat s prostředkem.

## <a name="what-if-an-sdk-asks-me-for-the-region-for-a-resource"></a>Co když SDK požádá o oblast pro určitý prostředek?

> [!WARNING]
> Služba Speech Services v tuto **chvíli nepodporuje vlastní** subdomény. Při použití služeb Speech a přidružených sad SDK prosím použijte místní koncové body.

Místní koncové body a názvy vlastních subdomén jsou podporovány a lze je použít zaměnitelné. Vyžaduje se ale plný koncový bod.

Informace o oblasti jsou k dispozici v okně **Přehled** pro váš prostředek v [Azure Portal](https://portal.azure.com). Úplný seznam regionálních koncových bodů najdete v tématu [seznam regionálních koncových](#is-there-a-list-of-regional-endpoints) bodů.

## <a name="are-custom-subdomain-names-regional"></a>Jsou názvy vlastních subdomén regionální?

Ano. Použití vlastního názvu subdomény nemění žádné místní aspekty vašeho prostředku Cognitive Services.

## <a name="what-are-the-requirements-for-a-custom-subdomain-name"></a>Jaké jsou požadavky na název vlastní subdomény?

Název vlastní subdomény je pro váš prostředek jedinečný. Název může obsahovat jenom alfanumerické znaky a `-` znak. musí mít délku 2 až 64 znaků a nemůže končit znakem `-` .

## <a name="can-i-change-a-custom-domain-name"></a>Můžu změnit vlastní název domény?

Ne. Po vytvoření vlastního názvu subdomény a jeho přidružení k prostředku ho nelze změnit.

## <a name="can-i-reuse-a-custom-domain-name"></a>Můžu znovu použít vlastní název domény?

Každý název vlastní subdomény je jedinečný, takže pokud chcete znovu použít název vlastní subdomény, který jste přiřadili Cognitive Services prostředku, budete muset odstranit existující prostředek. Po odstranění prostředku můžete znovu použít název vlastní subdomény.

## <a name="is-there-a-list-of-regional-endpoints"></a>Existuje seznam regionálních koncových bodů?

Ano. Toto je seznam regionálních koncových bodů, které můžete použít s prostředky Azure Cognitive Services.

> [!NOTE]
> Služba Translator a rozhraní API pro vyhledávání Bingu používat globální koncové body.

| Typ koncového bodu | Oblast | Koncový bod |
|---------------|--------|----------|
| Veřejná | Globální (Translator & Bing) | `https://api.cognitive.microsoft.com` |
| | Austrálie – východ | `https://australiaeast.api.cognitive.microsoft.com` |
| | Brazil South | `https://brazilsouth.api.cognitive.microsoft.com` |
| | Střední Kanada | `https://canadacentral.api.cognitive.microsoft.com` |
| | Střední USA | `https://centralus.api.cognitive.microsoft.com` |
| | Východní Asie | `https://eastasia.api.cognitive.microsoft.com` |
| | East US | `https://eastus.api.cognitive.microsoft.com` |
| | USA – východ 2 | `https://eastus2.api.cognitive.microsoft.com` |
| | Francie – střed | `https://francecentral.api.cognitive.microsoft.com` |
| | Indie – střed | `https://centralindia.api.cognitive.microsoft.com` |
| | Japan East | `https://japaneast.api.cognitive.microsoft.com` |
| | Jižní Korea – střed | `https://koreacentral.api.cognitive.microsoft.com` |
| | USA – středosever | `https://northcentralus.api.cognitive.microsoft.com` |
| | Severní Evropa | `https://northeurope.api.cognitive.microsoft.com` |
| | Jižní Afrika – sever | `https://southafricanorth.api.cognitive.microsoft.com` |
| | Středojižní USA | `https://southcentralus.api.cognitive.microsoft.com` |
| | Jihovýchodní Asie | `https://southeastasia.api.cognitive.microsoft.com` |
| | Spojené království – jih | `https://uksouth.api.cognitive.microsoft.com` |
| | USA – středozápad | `https://westcentralus.api.cognitive.microsoft.com` |
| | Západní Evropa | `https://westeurope.api.cognitive.microsoft.com` |
| | USA – západ | `https://westus.api.cognitive.microsoft.com` |
| | Západní USA 2 | `https://westus2.api.cognitive.microsoft.com` |
| US Gov | USA (Gov) – Virginia | `https://virginia.api.cognitive.microsoft.us` |
| Čína | Čína – východ 2 | `https://chinaeast2.api.cognitive.azure.cn` |
| | Čína – sever | `https://chinanorth.api.cognitive.azure.cn` |

## <a name="see-also"></a>Viz také

* [Co je Cognitive Services?](./what-are-cognitive-services.md)
* [Authentication](authentication.md)
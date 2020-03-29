---
title: Vlastní subdomény
titleSuffix: Azure Cognitive Services
description: Vlastní názvy subdomén pro každý prostředek služby Cognitive Service se vytvářejí prostřednictvím portálu Azure, Azure Cloud Shell nebo Azure CLI.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: erhopf
ms.openlocfilehash: 108e5ebfa34db711af4ff33cbf4fbba897b8947d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "73647690"
---
# <a name="custom-subdomain-names-for-cognitive-services"></a>Vlastní názvy subdomén pro služby Cognitive Services

Azure Cognitive Services používají vlastní názvy subdomén pro každý prostředek vytvořený prostřednictvím [portálu Azure](https://portal.azure.com), [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/)nebo Azure [CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). Na rozdíl od místních koncových bodů, které byly běžné pro všechny zákazníky v určité oblasti Azure, vlastní názvy subdomén jsou jedinečné pro prostředek. Vlastní názvy subdomén jsou nutné k povolení funkcí, jako je Azure Active Directory (Azure AD) pro ověřování.

## <a name="how-does-this-impact-existing-resources"></a>Jaký to má dopad na stávající zdroje?

Prostředky služeb Cognitive Services vytvořené před 1. Tyto koncové body budou fungovat s existujícími a novými prostředky.

Pokud chcete migrovat existující prostředek, abyste využili vlastní názvy subdomén, abyste mohli povolit funkce, jako je Azure AD, postupujte podle následujících pokynů:

1. Přihlaste se k portálu Azure a vyhledejte prostředek služeb Cognitive Services, ke kterému chcete přidat vlastní název subdomény.
2. V okně **Přehled** vyhledejte a vyberte **Generovat vlastní název domény**.
3. Tím se otevře panel s pokyny k vytvoření jedinečné vlastní subdomény pro váš prostředek.
   > [!WARNING]
   > Po vytvoření vlastního názvu subdomény **jej nelze** změnit.

## <a name="do-i-need-to-update-my-existing-resources"></a>Je nutné aktualizovat stávající prostředky?

Ne. Místní koncový bod bude nadále fungovat pro nové a stávající služby Cognitive Services a vlastní název subdomény je volitelný. I v případě, že je přidán název vlastní subdomény místní koncový bod bude pokračovat v práci s prostředkem.

## <a name="what-if-an-sdk-asks-me-for-the-region-for-a-resource"></a>Co když se mě sada SDK zeptá na oblast pro prostředek?

> [!WARNING]
> Služby Speech Services v tuto chvíli **nepodporují** vlastní subdomény. Při používání služby Speech Services a přidružených sad SDK použijte místní koncové body.

Místní koncové body a vlastní názvy subdomén jsou podporovány a lze je zaměnit. Je však vyžadován úplný koncový bod.

Informace o oblasti jsou dostupné v okně **Přehled** pro váš prostředek na [webu Azure Portal](https://portal.azure.com). Úplný seznam regionálních koncových bodů najdete v [tématu Existuje seznam regionálních koncových bodů?](#is-there-a-list-of-regional-endpoints)

## <a name="are-custom-subdomain-names-regional"></a>Jsou vlastní názvy subdomén místní?

Ano. Použití názvu vlastní subdomény nezmění žádné regionální aspekty vašeho prostředku služby Cognitive Services.

## <a name="what-are-the-requirements-for-a-custom-subdomain-name"></a>Jaké jsou požadavky na vlastní název subdomény?

Vlastní název subdomény je jedinečný pro váš prostředek. Název může obsahovat pouze alfanumerické znaky a `-` znak; musí mít délku 2 až 64 znaků `-`a nesmí končit znakem .

## <a name="can-i-change-a-custom-domain-name"></a>Mohu změnit vlastní název domény?

Ne. Po vytvoření vlastního názvu subdomény a přidružení k prostředku jej nelze změnit.

## <a name="can-i-reuse-a-custom-domain-name"></a>Mohu znovu použít vlastní název domény?

Každý vlastní název subdomény je jedinečný, takže chcete-li znovu použít vlastní název subdomény, který jste přiřadili k prostředku služeb Cognitive Services, budete muset odstranit existující prostředek. Po odstranění prostředku můžete znovu použít vlastní název subdomény.

## <a name="is-there-a-list-of-regional-endpoints"></a>Existuje seznam regionálních koncových bodů?

Ano. Toto je seznam místních koncových bodů, které můžete použít s prostředky Azure Cognitive Services.

> [!NOTE]
> Rozhraní API pro překladač textu a rozhraní API vyhledávání Bingu používají globální koncové body.

| Typ koncového bodu | Region (Oblast) | Koncový bod |
|---------------|--------|----------|
| Public | Globální (překladač text & Bing) | `https://api.cognitive.microsoft.com` |
| | Austrálie – východ | `https://australiaeast.api.cognitive.microsoft.com` |
| | Brazílie – jih | `https://brazilsouth.api.cognitive.microsoft.com` |
| | Střední Kanada | `https://canadacentral.api.cognitive.microsoft.com` |
| | USA – střed | `https://centralus.api.cognitive.microsoft.com` |
| | Východní Asie | `https://eastasia.api.cognitive.microsoft.com` |
| | USA – východ | `https://eastus.api.cognitive.microsoft.com` |
| | USA – východ 2 | `https://eastus2.api.cognitive.microsoft.com` |
| | Francie – střed | `https://francecentral.api.cognitive.microsoft.com` |
| | Indie – střed | `https://centralindia.api.cognitive.microsoft.com` |
| | Japonsko – východ | `https://japaneast.api.cognitive.microsoft.com` |
| | Jižní Korea – střed | `https://koreacentral.api.cognitive.microsoft.com` |
| | USA – středosever | `https://northcentralus.api.cognitive.microsoft.com` |
| | Severní Evropa | `https://northeurope.api.cognitive.microsoft.com` |
| | Jižní Afrika – sever | `https://southafricanorth.api.cognitive.microsoft.com` |
| | USA – středojih | `https://southcentralus.api.cognitive.microsoft.com` |
| | Jihovýchodní Asie | `https://southeastasia.api.cognitive.microsoft.com` |
| | Spojené království – jih | `https://uksouth.api.cognitive.microsoft.com` |
| | USA – středozápad | `https://westcentralus.api.cognitive.microsoft.com` |
| | Západní Evropa | `https://westeurope.api.cognitive.microsoft.com` |
| | USA – západ | `https://westus.api.cognitive.microsoft.com` |
| | USA – západ 2 | `https://westus2.api.cognitive.microsoft.com` |
| US Gov | USA (Gov) – Virginia | `https://virginia.api.cognitive.microsoft.us` |
| Čína | Čína východ 2 | `https://chinaeast2.api.cognitive.azure.cn` |
| | Čína – sever | `https://chinanorth.api.cognitive.azure.cn` |

## <a name="see-also"></a>Viz také

* [Jaké jsou kognitivní služby?](Welcome.md)
* [Ověřování](authentication.md)

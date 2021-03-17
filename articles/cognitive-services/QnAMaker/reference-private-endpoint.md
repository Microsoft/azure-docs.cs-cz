---
title: Postup nastavení privátního koncového bodu – QnA Maker
description: Informace o vytváření privátních koncových bodů najdete ve spravovaném QnA Maker.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 01/12/2021
ms.openlocfilehash: 7907c81e45680de49f6653891fb4204a59db1002
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101710545"
---
# <a name="private-endpoints"></a>Privátní koncové body

Privátní koncový bod Azure je síťové rozhraní, které vás privátně a zabezpečeně připojí ke službám využívajícím službu Azure Private Link. Nyní QnA Maker poskytuje podporu pro vytváření privátních koncových bodů ke službě Azure Search. Tato funkce je k dispozici ve spravovaném QnA Maker. 

Soukromé koncové body poskytuje služba [Azure Private Link](../../private-link/private-link-overview.md)jako samostatnou službu. Další informace o nákladech najdete na [stránce s cenami.](https://azure.microsoft.com/pricing/details/private-link/) 

## <a name="prerequisites"></a>Požadavky
> [!div class="checklist"]
> * Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/cognitive-services/) před tím, než začnete.
> * QnA Maker [Spravovaný prostředek](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) vytvořený v Azure Portal. Mějte na paměti, že vaše Azure Active Directory ID, předplatné a název prostředku QnA jste zvolili při vytváření prostředku.

## <a name="steps-to-enable-private-endpoint"></a>Postup povolení privátního koncového bodu
1. Přiřaďte roli *přispívat* k QnA maker spravované služby v instanci služby Azure Search. Tato operace vyžaduje přístup *vlastníka* k předplatnému. Pro získání identity použijte kartu Identita v prostředku služby.
![Identita spravované služby](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-identity.png)

2. Přidejte výše uvedenou identitu jako *přispívat* na kartu Azure Search Service IAM. ![ Služba IAM spravované služby](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-access-control.png)

3. Klikněte na *Přidat přiřazení rolí*, přidejte identitu a klikněte na *Uložit*.
![Přiřazení spravované role](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-role-assignment.png)

4. Nyní přejděte na kartu *sítě* v instanci služby Azure Search a přepněte data o připojení koncových bodů z *veřejné* na *soukromou*. Tato operace je dlouhodobě spuštěná a může trvat až 30 minut, než se dokončí. 
![Spravované sítě služby Azure Search](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking.png)

5. Přejděte na kartu *sítě* QnA maker spravovaná služba a v části *Povolení přístupu z* vyberte možnost *vybrané sítě a privátní koncové body* a klikněte na *Uložit*. 
![QnA maker spravované newtorking](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking-2.png)

Tím se vytvoří propojení privátního koncového bodu mezi službou QnA maker a instancí služby Azure vnímání Search Service. Připojení privátního koncového bodu můžete ověřit na kartě *síť* Azure Search instance služby. Po dokončení celé operace je vhodné použít službu QnA Maker. 
![Spravovaná síťová služba](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking-3.png)


## <a name="support-details"></a>Podrobnosti o podpoře
 * Po povolení privátního přístupu ke službě Qnamakerem nepodporujeme službu Change Azure Search Service. Pokud po povolení privátního přístupu změníte službu Azure Search přes kartu Konfigurace, služba Qnamakerem přestane fungovat.
 * Když nakonfigurujete připojení privátního koncového bodu, pokud přepnete Azure Search síťové služby na Public, nebudete moct službu Qnamakerem používat. Aby připojení privátního koncového bodu fungovalo, musí být síťové služby Azure Search privátní.
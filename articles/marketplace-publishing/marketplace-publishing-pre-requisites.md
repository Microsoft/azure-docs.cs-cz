---
title: "Netechnické požadavky pro vytváření nabídku pro Azure Marketplace | Microsoft Docs"
description: "Pochopili požadavky na vytvoření a nasazení nabídku pro Azure Marketplace pro ostatní k nákupu."
services: marketplace-publishing
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
editor: 
ms.assetid: 3dae463b-8f48-4f52-8fa8-4e3975f09f43
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 08/18/2016
ms.author: mbaldwin
ms.openlocfilehash: 5c30e62bf345843fe83b3f17b728e1a937d19ce3
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="general-prerequisites-for-creating-an-offer-for-the-azure-marketplace"></a>Obecné požadavky pro vytváření nabídku pro Azure Marketplace
Pochopte Obecné, proces zaměřené na obchodní požadavky, které jsou potřeba jak urychlit proces vytvoření nabídky.

## <a name="ensure-that-you-are-registered-as-a-seller-with-microsoft"></a>Ujistěte se, že jste zaregistrováni jako prodejce se společností Microsoft
Podrobné pokyny k registraci prodejce účet Microsoft, přejděte na [vytváření účtů a registrace](marketplace-publishing-accounts-creation-registration.md).

* **Pokud vaše společnost je již zaregistrován jako prodejce na webu Dev Center a chcete vytvořit novou nabídku,** pak přihlášení k publikování se stejným id e-mailu, pomocí které Dev Center se provádí registraci portálu. Tento krok je povinný, takže jsou vzájemně propojené portálu Dev Center a publikování.
* **Pokud vaše společnost je již zaregistrován jako prodejce na webu Dev Center a chcete upravit existující nabídku,** pak buď přihlášení k publikování portálu pomocí účtu správce nebo pomocí účtu, který se přidal jako spolusprávce v publikační portálu. Postup přidání spolusprávce účtu je uveden níže.

## <a name="steps-to-add-a-co-admin-in-the-publishing-portal"></a>Postup pro přidání spolusprávce publikování portálu
Správci publikování portálu můžete přidat ostatním členům společnosti, kteří pracují na aplikaci, jako spolusprávce v publikační portálu. **Za předpokladu, že jste správcem,** níže uvedené kroky přidejte ko-správce.

> [!NOTE]
> Pro nové uživatele, než přidáte spolusprávce v publikační portál, ujistěte se, že jste vytvořili aspoň jednu aplikaci v publikační portálu. To je potřeba jako **VYDAVATELŮ** kartě se zobrazují pouze po vytvoření alespoň jedna aplikace v publikační portálu.
> 
> 

1. Zajistěte, aby byl identifikátor e-mailu spolusprávce account(MSA) společnosti Microsoft. Pokud ne, zaregistrujte ji jako účet, pomocí této [odkaz](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1).
2. Zkontrolujte, zda je alespoň jedna aplikace pod účtem správce, než se pokusíte přidat ko-správce.
3. Poté, co se dokončí výše uvedené kroky, přihlášení k publikování portálu s id spolusprávce e-mailu a pak při odhlášení.
4. Teď přihlášení k publikování portálu s id e-mailu správce.
5. Přejděte do vydavatele -> vyberte -> váš účet správce -> Přidat spolusprávce (snímek vypsáni níže)
   
    ![Kreslení](media/marketplace-publishing-pre-requisites/imgAddAdmin_05.png)
6. Zajištěna ID e-mailu uvedených v různých fázích proces publikování (například Dev Center, publikování portálu) pro všechny komunikaci od společnosti Microsoft.
7. Pro registraci Dev Center nepoužívejte účet přidružený jedna osoba. To je navržený pro odebrání závislostí ze jedna osoba.
8. Pokud jste čelí všechny problémy s registrací Dev Center, pak vyvolejte lístek pomocí této [odkaz](https://developer.microsoft.com/en-us/windows/support).

## <a name="steps-to-delete-a-co-admin-in-the-publishing-portal"></a>Postup odstranění spolusprávce v publikační portálu
**Za předpokladu, že jste správcem,** níže uvedené kroky odstranit co správce.

1. Přihlášení k publikování portálu s id e-mailu správce.
2. Přejděte na **vydavatelů** -> vyberte -> váš účet **správci** -> **Spolusprávci**.
3. Klikněte na **X** tlačítko vedle spolusprávce chcete odstranit tot (snímek vypsáni níže).
   
    ![Kreslení](media/marketplace-publishing-pre-requisites/imgDeleteAdmin_03.png)

> [!IMPORTANT]
> Není nutné k dokončení daň a bank informace společnosti, pokud máte v úmyslu publikovat pouze volné nabídky (nebo přineste vlastní licence).
> 
> Abyste mohli začít se musí dokončit registraci společnosti. Ale když vaše společnost pracuje na informace o daň a bank v účtu Microsoft Developer, vývojáři začít pracovat na vytvoření bitové kopie virtuálního počítače v [publikování portál](https://publish.windowsazure.com), načítání certifikaci a testování je v pracovní prostředí Azure. Budete potřebovat účet schválení dokončení prodejce pouze u poslední krok publikování vaši nabídku pro Azure Marketplace.
> 
> 

## <a name="acquire-an-azure-pay-as-you-go-subscription"></a>Získat předplatné Azure "průběžnými platbami"
Toto je odběr, který použijete k vytvoření bitové kopie virtuálních počítačů a ruční přes obrázky, které chcete [Azure Marketplace](https://azure.microsoft.com/marketplace/). Pokud nemáte předplatné existující, pak zaregistrujte v https://account.windowsazure.com/signup?offer=ms-azr-0003p.

## <a name="sell-from-countries"></a>"Zákazník – od" zemích
> [!WARNING]
> Chcete-li prodávat vaší služby v Azure Marketplace, musí Ujistěte se, že vaše registrovaná entita je z jednoho z zemí schválené "zákazník – od". Toto omezení je výběr a zdanění z důvodů. Aktivně chceme v blízké budoucnosti rozbalte tento seznam zemí, tak si Nenechte ujít. Úplný seznam najdete v části 1b z [zásady zapojení Azure Marketplace](http://go.microsoft.com/fwlink/?LinkID=526833).
> 
> 

## <a name="next-steps"></a>Další postup
Jakmile jsou splněny netechnické požadavky, jsou vedle nabídku konkrétní technické požadavky. Kliknutím na odkaz na článek pro příslušné nabídka typ, který chcete vytvořit pro Azure Marketplace.

* [Požadavky technické virtuálních počítačů](marketplace-publishing-vm-image-creation-prerequisites.md)
* [Řešení šablony technické požadavky](marketplace-publishing-solution-template-creation-prerequisites.md)

## <a name="see-also"></a>Další informace najdete v tématech
* [Začínáme: postup publikování nabídky pro Azure Marketplace](marketplace-publishing-getting-started.md)


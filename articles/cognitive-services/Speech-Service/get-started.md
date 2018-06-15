---
title: Vyzkoušení služby řeči zdarma | Microsoft Docs
description: Zjistit, jak můžete použít službu řeči bez nákladů.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/17/2018
ms.author: v-jerkin
ms.openlocfilehash: 3feef04694336d9173b419285a96fcaef543e18f
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "35343958"
---
# <a name="try-the-speech-service-for-free"></a>Vyzkoušení služby řeči zdarma

Začínáme se službou řeči je jednoduché a dostupné. 30denní bezplatné zkušební verze umožňuje zjistit, co můžete službu provést a rozhodnout, zda je správný pro potřeby vaší aplikace.

Pokud potřebujete více času, zaregistrujte si účet Microsoft Azure – se dodává s 200 USD v kompenzace, které můžete použít k placené předplatné služby řeči po dobu 30 dnů.

Nakonec nabízí Služba rozpoznávání řeči volné, menší vrstvu vhodný pro vývoj aplikací. Tato bezplatná registrace můžete zachovat i po vypršení platnosti vaší platební služby.

## <a name="free-trial"></a>Bezplatná zkušební verze

30denní bezplatné zkušební verze umožňuje přístup k cenová úroveň a po omezenou dobu standard S0. Chcete-li si zaregistrovat bezplatnou 30denní zkušební verzi, postupujte takto.

1. Přejděte na [zkuste kognitivní služby](https://azure.microsoft.com/try/cognitive-services/) stránky.

1. Přepněte na kartu řeči a klepněte **klíč rozhraní API získat** tlačítko vedle "Řeči služby".

   ![Karta služby řeči](media/index/try-speech-api-free-trial1.png)<br>
   ![Klíč rozhraní API](media/index/try-speech-api-free-trial2.png)

3. Přijmout podmínky a z rozevírací nabídky vyberte národní prostředí.

   ![Přijmout podmínky](media/index/try-speech-api-free-trial3.png)

4. Přihlaste se pomocí účtu Microsoft, Facebook, LinkedIn nebo Githubu. Nebo může zaregistrovat bezplatný účet Microsoft:

    * Přejděte na [portálu účtu Microsoft](https://account.microsoft.com/account).
    * Klikněte na tlačítko **přihlásit pomocí Microsoft**.

    ![Přihlášení](media/index/try-speech-api-free-trial4.png)

    * Pokud budete vyzváni k přihlášení, klikněte na tlačítko "Vytvořit jednu".

    ![Vytvořit nový účet](media/index/try-speech-api-free-trial5.png)

    * V kroky, které řídí, zadejte e-mailovou adresu nebo telefonní číslo přiřaďte heslo a postupujte podle pokynů k ověření svůj nový účet Microsoft.

Po přihlášení, začne používat bezplatnou zkušební verzi. Zobrazené webové stránky obsahuje seznam všech kognitivní služeb, pro které máte aktuálně zkušebních předplatných. Jsou uvedeny dva klíče předplatné vedle "Řeči služby." Můžete použít buď klíč ve svých aplikacích.

> [!NOTE]
> Všechny bezplatné zkušební verze předplatného jsou v oblasti západní USA. Nezapomeňte použít koncový bod odpovídající vaší oblasti při provádění požadavků.

## <a name="new-azure-account"></a>Nový účet Azure

Nové účty Azure přijímat 200 USD platební služby, který trvá po dobu 30 dnů. Tento platební lze dále prozkoumat službu řeči nebo začátek vývoje aplikace.

Chcete-li si zaregistrovat nový účet Azure, postupujte takto.

1. Přejděte na [přihlašovací stránku služby Azure](https://azure.microsoft.com/free/ai/). 

1. Klikněte na tlačítko **spustit volné**.

    ![Začněte zdarma](media/index/try-speech-api-new-azure1.png)

3. Přihlaste se pomocí účtu Microsoft. Pokud nemáte:

    * Přejděte na [portálu účtu Microsoft](https://account.microsoft.com/account).
    * Klikněte na tlačítko **přihlásit pomocí Microsoft**.
    * Pokud budete vyzváni k přihlášení, klikněte na tlačítko "Vytvořit jednu".
    * V kroky, které řídí, zadejte e-mailovou adresu nebo telefonní číslo přiřaďte heslo a postupujte podle pokynů k ověření svůj nový účet Microsoft.

1. Zadejte zbývající požadované informace se přihlásit k účtu. Zadejte vaší země a vaše jméno a zadejte telefonní číslo a e-mailovou adresu.

    ![Zadejte informace o](media/index/try-speech-api-new-azure2.png)

    Ověřit vaši identitu po telefonu a tím, že poskytuje číslo platební karty a přijetí smlouvy o Azure uživatele. (Platební karty nebudou platit.)

    ![Přijměte smlouvu](media/index/try-speech-api-new-azure3.png)

Se vytvoří účet Azure zdarma. Postupujte podle kroků v další části, kde spustit předplatné služby řeči.

## <a name="create-a-speech-resource-in-azure"></a>Vytvořte prostředek řeči v Azure

Prostředek služby řeči přidáte ke svému účtu Azure, postupujte podle těchto kroků.

1. Přihlaste se k [portál Azure](https://ms.portal.azure.com/) pomocí účtu Microsoft.

1. Klikněte na tlačítko **vytvořit prostředek** (zeleným **+** ikonu) v levém horním rohu portálu.

    ![Vytvořit prostředek](media/index/try-speech-api-create-speech1.png)

1. Vyhledejte řeči v novém okně.

    ![Klikněte na tlačítko řeči](media/index/try-speech-api-create-speech2.png)

1. Ve výsledcích hledání klikněte na tlačítko "Řeči (preview)."

1. Klikněte **vytvořit** tlačítko v dolní části panelu řeči služby.

    ![Kliknutím na vytvořit](media/index/try-speech-api-create-speech3.png)

1. V panelu vytvořit zadejte:

    * Název pro nový prostředek. Název umožňuje rozlišit mezi více předplatných na stejnou službu.
    * Zvolte předplatné Azure, který nový prostředek je přidružený k určení, jak se účtují poplatky.
    * Vyberte oblast, kde se použije k prostředku. Služba rozpoznávání řeči je v současné době dostupná v oblastech východní Asie, Severní Evropa a západní USA.
    * Vyberte cenovou úroveň, buď F0 (omezeným bezplatné předplatné) nebo S0 (standardní předplatné). Klikněte na tlačítko **zobrazení úplné podrobnosti o cenách** úplné informace o cenách a využití kvóty pro každou úroveň.
    * Vytvořit novou skupinu prostředků pro toto předplatné řeči nebo ji přiřadit stávající. Nápověda, vytvořit si různých předplatných Azure uspořádané skupiny zdrojů.
    * Pohodlný přístup k vašemu předplatnému v budoucnu, označte **připnout na řídicí panel** zaškrtávací políčko.
    * Klikněte na tlačítko **vytvořit.**

    ![Kliknutím na vytvořit panelu](media/index/try-speech-api-create-speech4.png)

    Ho může chvíli trvat, vytvoření a nasazení nového prostředku řeči. Rychlý start panelu se zobrazí s informacemi o nový prostředek.

    ![Panel rychlý start](media/index/try-speech-api-create-speech5.png)

1. Klikněte **klíče** odkaz v kroku 1 v panelu Rychlý start zobrazíte klíče pro předplatné. Každé předplatné má dva klíče; můžete použít buď ve vaší aplikaci. Klikněte na tlačítko vedle každého klíče a zkopírujte ho do schránky pro vložení do vašeho kódu.

> [!NOTE]
> Můžete vytvořit libovolný počet úroveň standard odběry v jedné nebo více oblastech. Můžete však vytvořit jenom jedno předplatné volné vrstvy.

## <a name="next-steps"></a>Další postup

Stáhněte si sadu SDK a ukázkový kód docházet k rozpoznávání řeči služby.

> [!div class="nextstepaction"]
> [Rozpoznávání řeči sady SDK](speech-sdk.md)

> [!div class="nextstepaction"]
> [Ukázka kódu](samples.md)

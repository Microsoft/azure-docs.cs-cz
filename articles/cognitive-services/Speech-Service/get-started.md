---
title: Vyzkoušejte si službu Speech zdarma
description: Zjistěte, jak se můžete pokusit Speech service nevztahují žádné poplatky.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/17/2018
ms.author: v-jerkin
ms.openlocfilehash: ff9a258d42f25ded82545909cdeade119548148e
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/27/2018
ms.locfileid: "39325203"
---
# <a name="try-the-speech-service-for-free"></a>Vyzkoušejte si službu Speech zdarma

Začínáme se službou Speech service je snadné a cenově dostupné. 30denní bezplatná zkušební verze vám umožní zjistit, co může služba provádět a rozhodnout, jestli je vhodná pro potřeby vaší aplikace.

Pokud potřebujete více času, zaregistrujte si účet Microsoft Azure – jde o s 200 USD v kredit služby, které můžete použít na placené předplatné služby řeči po dobu až 30 dnů.

Nakonec Speech service nabízí na úrovni free, s nízkým objemem vhodný pro vývoj aplikací. Abyste mohli tuto bezplatnou registraci i po vypršení platnosti vašeho kreditu služby.

## <a name="free-trial"></a>Bezplatná zkušební verze

30denní bezplatná zkušební verze poskytuje přístup k po omezenou dobu cenová úroveň standard S0. Zaregistrovat 30denní bezplatnou zkušební verzi, postupujte podle těchto kroků.

1. Přejděte [zkuste služby Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) stránky.

1. Přepněte na kartu řeči a klikněte na tlačítko **klíč rozhraní API získat** tlačítko vedle "Hlasové služby".

   ![Karta služby řeči](media/index/try-speech-api-free-trial1.png)<br>
   ![Klíč rozhraní API](media/index/try-speech-api-free-trial2.png)

3. Souhlas s podmínkami a z rozevírací nabídky vyberte národní prostředí.

   ![Souhlas s podmínkami](media/index/try-speech-api-free-trial3.png)

4. Přihlaste se pomocí účtu Microsoft, Facebook, LinkedIn nebo GitHub. Nebo se může zaregistrovat si bezplatný účet Microsoft:

    * Přejděte [portálu účtů Microsoft](https://account.microsoft.com/account).
    * Klikněte na tlačítko **přihlásit se účtem Microsoft**.

    ![Přihlášení](media/index/try-speech-api-free-trial4.png)

    * Když se zobrazí výzva k přihlášení, klikněte na možnost "Vytvořit."

    ![Vytvořit nový účet](media/index/try-speech-api-free-trial5.png)

    * V krocích, které řídí, zadejte e-mailovou adresu nebo telefonní číslo přiřaďte heslo a postupujte podle pokynů k ověření svůj nový účet Microsoft.

Po přihlášení, začne bezplatné zkušební verze. Zobrazené webové stránky obsahuje seznam všech služeb Cognitive Services, u které máte aktuálně zkušebních verzí předplatného. Jsou uvedeny dva klíče předplatného vedle "Hlasové služby." Ani jeden klíč můžete použít ve svých aplikacích.

> [!NOTE]
> Všechny bezplatných zkušebních verzí předplatného se v oblasti západní USA. Nezapomeňte použít odpovídající vaší oblasti při zasílání požadavků koncový bod.

## <a name="new-azure-account"></a>Nový účet Azure

Nové účty Azure zobrazí služba kreditem 200 USD, který trvá až 30 dnů. Tento kredit je možné dále prozkoumat Speech service nebo začátek vývoje aplikace.

Si zaregistrovat nový účet Azure, postupujte podle těchto kroků.

1. Přejděte [přihlašovací stránku služby Azure](https://azure.microsoft.com/free/ai/). 

1. Klikněte na tlačítko **začít zdarma**.

    ![Začněte zdarma](media/index/try-speech-api-new-azure1.png)

3. Přihlaste se pomocí svého účtu Microsoft. Pokud nemáte:

    * Přejděte [portálu účtů Microsoft](https://account.microsoft.com/account).
    * Klikněte na tlačítko **přihlásit se účtem Microsoft**.
    * Když se zobrazí výzva k přihlášení, klikněte na možnost "Vytvořit."
    * V krocích, které řídí, zadejte e-mailovou adresu nebo telefonní číslo přiřaďte heslo a postupujte podle pokynů k ověření svůj nový účet Microsoft.

1. Zadejte zbývající informace požadované k registraci pro účet. Zadejte svou zemi a vaše jméno a zadejte telefonní číslo a e-mailovou adresu.

    ![Zadejte informace](media/index/try-speech-api-new-azure2.png)

    Ověřit vaši identitu po telefonu a tím, že poskytuje číslo kreditní karty a potom přijměte podmínky smlouvy Azure uživatele. (Vaši kreditní kartu nebude nic účtováno.)

    ![Přijměte podmínky smlouvy](media/index/try-speech-api-new-azure3.png)

Vytvoření bezplatného účtu Azure. Postupujte podle kroků v další části, kde zahájíte předplatné ke službě řeči.

## <a name="create-a-speech-resource-in-azure"></a>Vytvořit prostředek řeči v Azure

Přidat prostředek služby řeči ke svému účtu Azure, postupujte podle těchto kroků.

1. Přihlaste se k [webu Azure portal](https://ms.portal.azure.com/) pomocí účtu Microsoft.

1. Klikněte na tlačítko **vytvořit prostředek** (zelený **+** ikonu) v levém horním rohu portálu.

    ![Vytvořit prostředek](media/index/try-speech-api-create-speech1.png)

1. V novém okně vyhledejte řeči.

    ![Klikněte na řeč](media/index/try-speech-api-create-speech2.png)

1. Ve výsledcích hledání klikněte na tlačítko "Řeči (preview)."

1. Klikněte na tlačítko **vytvořit** tlačítko v dolní části panelu služby řeči.

    ![Klikněte na vytvořit](media/index/try-speech-api-create-speech3.png)

1. Na panelu vytvoření zadejte:

    * Název pro nový prostředek. Název umožňuje rozlišit několik předplatných ve stejné službě.
    * Zvolte předplatné Azure, se nový prostředek je přidružený k určení, jak se účtují poplatky.
    * Vyberte oblast, kde se budou používat zdroj. Speech service je aktuálně dostupná v oblastech východní Asie, Severní Evropa a západní USA.
    * Vyberte cenovou úroveň buď F0 (omezené bezplatné předplatné) nebo S0 (standardní předplatné). Klikněte na tlačítko **zobrazit všechny podrobnosti o cenách** úplné informace o cenách a využití kvóty pro jednotlivé úrovně.
    * Vytvořit novou skupinu prostředků pro toto předplatné řeči nebo přiřadit některý z existujících. Pomáhají udržovat vaše různých předplatných Azure, které jsou uspořádány skupiny zdrojů.
    * Pohodlný přístup k vašemu předplatnému v budoucnu, označte **připnout na řídicí panel** zaškrtávací políčko.
    * Klikněte na tlačítko **vytvořit.**

    ![Klikněte na vytvořit panel](media/index/try-speech-api-create-speech4.png)

    To může chvíli trvat, vytvářet a nasazovat vaše nový prostředek řeči. Otevře se panel rychlý start s informace o nový prostředek.

    ![Rychlý start panel](media/index/try-speech-api-create-speech5.png)

1. Klikněte na tlačítko **klíče** odkaz v kroku 1 v panelu Rychlý start k zobrazení klíče předplatného. Každé předplatné má dva klíče; můžete použít buď ve vaší aplikaci. Klikněte na tlačítko vedle každého klíče ho zkopírovat do schránky pro vkládání do kódu.

> [!NOTE]
> V jedné nebo několika oblastí může vytvořit libovolný počet předplatných úrovně standard. Můžete však vytvořit pouze jedna bezplatná úroveň předplatného.

## <a name="next-steps"></a>Další postup

Proveďte jednu z našich šablon rychlý start během 10 minut nebo projděte si naše ukázky SDK.

> [!div class="nextstepaction"]
> [Rychlý start: rozpoznávat řeč v jazyce C#](quickstart-csharp-dotnet-windows.md)
> [ukázky řeči SDK](speech-sdk.md#get-the-samples)

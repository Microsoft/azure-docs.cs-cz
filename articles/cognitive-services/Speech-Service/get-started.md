---
title: Vyzkoušejte si službu Speech zdarma
description: Zjistěte, jak se můžete pokusit Speech service nevztahují žádné poplatky.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/17/2018
ms.author: v-jerkin
ms.openlocfilehash: a0ff6c09eb0a6fffe0ce82fe9ccb3bc43970ad68
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2018
ms.locfileid: "44024614"
---
# <a name="try-the-speech-service-for-free"></a>Vyzkoušejte si službu Speech zdarma

Začínáme se službou Speech service je snadné a cenově dostupné. 30denní bezplatná zkušební verze vám umožní zjistit, co může služba provádět a rozhodnout, jestli je vhodná pro potřeby vaší aplikace.

Pokud potřebujete více času, zaregistrujte si účet Microsoft Azure – jde o s 200 USD v kredit služby, které můžete použít na placené předplatné služby řeči po dobu až 30 dnů.

Nakonec Speech service nabízí na úrovni free, s nízkým objemem, který je vhodný pro vývoj aplikací. Abyste mohli tuto bezplatnou registraci i po vypršení platnosti vašeho kreditu služby.

## <a name="free-trial"></a>Bezplatná zkušební verze

30denní bezplatná zkušební verze poskytuje přístup k po omezenou dobu cenová úroveň standard S0. 

Chcete-li zaregistrovat 30denní bezplatnou zkušební verzi:

1. Přejděte na [vyzkoušet služby Cognitive Services](https://azure.microsoft.com/try/cognitive-services/).

1. Vyberte **rozhraní API pro rozpoznávání řeči** kartu.

   ![Karta služby řeči](media/index/try-speech-api-free-trial1.png)
   
1. V části **hlasové služby <sup>ve verzi PREVIEW</sup>**, vyberte **získat klíč rozhraní API** tlačítko.

   ![Klíč rozhraní API](media/index/try-speech-api-free-trial2.png)

1. Souhlas s podmínkami a z rozevírací nabídky vyberte národní prostředí.

   ![Souhlas s podmínkami](media/index/try-speech-api-free-trial3.png)

1. Přihlaste se pomocí účtu Microsoft, Facebook, LinkedIn nebo GitHub. Nebo si můžete zaregistrovat si bezplatný účet Microsoft:

    * Přejděte [portálu účtů Microsoft](https://account.microsoft.com/account).
    * Vyberte **přihlásit se účtem Microsoft**.

    ![Přihlášení](media/index/try-speech-api-free-trial4.png)

    * Když se zobrazí výzva k přihlášení, vyberte **vytvořit**.

    ![Vytvořit nový účet](media/index/try-speech-api-free-trial5.png)

    * V krocích, které řídí, zadejte e-mailovou adresu nebo telefonní číslo přiřaďte heslo a postupujte podle pokynů k ověření svůj nový účet Microsoft.

Po přihlášení, začne bezplatné zkušební verze. Zobrazená webová stránka obsahuje seznam všech služeb Azure Cognitive Services, pro které máte aktuálně zkušebních verzí předplatného. Vedle položky jsou uvedeny dva klíče předplatného **hlasové služby**. Ani jeden klíč můžete použít ve svých aplikacích.

> [!NOTE]
> Všechny bezplatných zkušebních verzí předplatného se v oblasti západní USA. Pokud provedete požadavky, je potřeba použít koncový bod, který odpovídá vaší oblasti.

## <a name="new-azure-account"></a>Nový účet Azure

Nové účty Azure získáte kredit 200 USD, služby, který je k dispozici po dobu až 30 dnů. Tento kredit můžete podrobněji prozkoumat Speech service nebo začátek vývoje aplikace.

Chcete-li zaregistrovat nový účet Azure:

1. Přejděte [Azure registrační stránku](https://azure.microsoft.com/free/ai/). 

1. Vyberte **začít zdarma**.

    ![Začněte zdarma](media/index/try-speech-api-new-azure1.png)

1. Přihlaste se pomocí svého účtu Microsoft. Pokud nemáte:

    * Přejděte [portálu účtů Microsoft](https://account.microsoft.com/account).
    * Vyberte **přihlásit se účtem Microsoft**.
    * Když se zobrazí výzva k přihlášení, vyberte **ho vytvořit.**
    * V krocích, které řídí, zadejte e-mailovou adresu nebo telefonní číslo přiřaďte heslo a postupujte podle pokynů k ověření svůj nový účet Microsoft.

1. Zadejte zbývající informace, které vyžaduje se přihlásit k účtu. Zadejte svou zemi a vaše jméno a zadejte telefonní číslo a e-mailovou adresu.

    ![Zadejte informace](media/index/try-speech-api-new-azure2.png)

    Ověřte vaši identitu po telefonu a tím, že poskytuje číslo kreditní karty. (Vaši kreditní kartu nebude nic účtováno.) Poto, přijměte podmínky smlouvy Azure uživatele. 

    ![Přijměte podmínky smlouvy](media/index/try-speech-api-new-azure3.png)

Vytvoření bezplatného účtu Azure. Postupujte podle kroků v další části, kde zahájíte předplatné ke službě řeči.

## <a name="create-a-speech-resource-in-azure"></a>Vytvořit prostředek řeči v Azure

Chcete-li přidat prostředek služby řeči ke svému účtu Azure:

1. Přihlaste se k [webu Azure portal](https://ms.portal.azure.com/) pomocí účtu Microsoft.

1. Vyberte **vytvořit prostředek** v levém horním rohu portálu.

    ![Vytvoření prostředku](media/index/try-speech-api-create-speech1.png)

1. V **nový** okna, vyhledejte **řeči**.

1. Ve výsledcích hledání vyberte **řeči (preview)**.

    ![Výběr řeči (preview)](media/index/try-speech-api-create-speech2.png)

1. V části **řeči (preview)**, vyberte **vytvořit** tlačítko.

    ![Vyberte tlačítko Vytvořit](media/index/try-speech-api-create-speech3.png)

1. V části **vytvořit**, zadejte:

    * Název pro nový prostředek. Název umožňuje rozlišit několik předplatných ve stejné službě.
    * Zvolte předplatné Azure, se nový prostředek je přidružený k určení, jak se účtují poplatky.
    * Vyberte oblast, kde se budou používat zdroj. Speech service je aktuálně dostupná v oblastech východní Asie, Severní Evropa a západní USA.
    * Vyberte cenovou úroveň buď F0 (omezené bezplatné předplatné) nebo S0 (standardní předplatné). Vyberte **zobrazit všechny podrobnosti o cenách** podrobnější informace o cenách a využití kvóty pro jednotlivé úrovně.
    * Vytvořit novou skupinu prostředků pro toto předplatné řeči nebo předplatné přiřadit existující skupinu prostředků. Pomáhají udržovat vaše různých předplatných Azure, které jsou uspořádány skupiny zdrojů.
    * Pohodlný přístup k vašemu předplatnému v budoucnu, vyberte **připnout na řídicí panel** zaškrtávací políčko.
    * Vyberte **vytvořit.**

    ![Vyberte tlačítko Vytvořit](media/index/try-speech-api-create-speech4.png)

    Může trvat okamžiku vytvoření a nasazení vašeho nového prostředku řeči. Vyberte **rychlý Start** zobrazíte informace o nový prostředek.

    ![Rychlý start panel](media/index/try-speech-api-create-speech5.png)

1. V části **rychlý Start**, vyberte **klíče** odkaz v kroku 1, chcete-li zobrazit klíče předplatného. Každé předplatné má dva klíče; ani jeden klíč můžete použít ve vaší aplikaci. Klikněte na tlačítko vedle každého klíče ho zkopírovat do schránky pro vkládání do kódu.

> [!NOTE]
> Neomezený počet předplatných úrovně standard můžete vytvořit v jedné nebo víc oblastech. Můžete však vytvořit jenom jedno předplatné bezplatné úrovně.

## <a name="next-steps"></a>Další postup

Proveďte jeden z našich šablon rychlý start během 10 minut nebo projděte si naše ukázky SDK:

> [!div class="nextstepaction"]
> [Rychlý start: Rozpoznávat řeč v jazyce C#](quickstart-csharp-dotnet-windows.md)
> [ukázky sadou SDK pro řeč](speech-sdk.md#get-the-samples)

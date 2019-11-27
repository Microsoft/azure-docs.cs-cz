---
title: Vyzkoušejte si službu Speech zdarma
titleSuffix: Azure Cognitive Services
description: Začínáme se službou Speech je snadná a cenově dostupná. K dispozici jsou dvě možnosti zdarma, abyste mohli zjistit, co služba může udělat, a rozhodnout se, jestli je to pro vaše potřeby správné.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.custom: seodec18, seo-javascript-october2019
ms.openlocfilehash: 30bdbf9fa0ea346892622c3e7f24f9f31652a650
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280495"
---
# <a name="try-the-speech-service-for-free"></a>Vyzkoušejte si službu Speech zdarma

Používání služby Speech je snadné a cenově dostupné. K dispozici jsou dvě možnosti zdarma, abyste mohli zjistit, co služba může udělat, a rozhodnout se, jestli je to pro vaše potřeby správné:

- Získejte bezplatnou zkušební verzi bez zadání informací o kreditních kartách (musíte mít existující účet Azure).
- Vytvoření nového účtu Azure zdarma za zkušební období (vyžaduje se informace o kreditní kartě)

V tomto článku zvolíte jednu z těchto možností, které nejlépe vyhovují vašim potřebám.

> [!NOTE]
> Služba Speech má dvě úrovně služeb: Free a Subscription, které mají různá omezení a výhody. Když si zaregistrujte bezplatný účet Azure, najdete ho v kreditu služby $200, který můžete použít k placenému předplatnému služby Speech Service, které platí až po dobu až 30 dnů.
>
> Pokud používáte bezplatnou úroveň služby řeči s nízkým objemem, můžete si toto bezplatné předplatné ponechat i po vypršení bezplatné zkušební verze nebo kreditu služby.
>
> Další informace najdete v tématu [Cognitive Services Price Speech Services](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

## <a name="try-the-speech-service-without-credit-card-info"></a>Vyzkoušet službu Speech bez informací o kreditní kartě

I když doporučujeme vyzkoušet službu Speech Service podle pokynů v následující části, můžete dát přednost pokyny této části, pokud platí následující:

- Už máte aktivní účet Azure.
- Chcete vyhodnotit službu Speech bez vytváření nového účtu Azure.
- Dáváte přednost, že platební kartu není potřeba a po zkušební době se neukládají žádná data.

> [!NOTE]
> Zkušební období se spustí hned po dokončení následujících kroků.

1. Přejít na [vyzkoušet službu rozpoznávání](https://azure.microsoft.com/try/cognitive-services/).
1. Vyberte kartu **rozhraní API pro rozpoznávání řeči** .
1. Vyberte **získat klíč rozhraní API**.

Budou vám nabídnuty možnosti fakturace. Zvolte možnost Free a pak si přečtěte a schvalte smlouvu uživatele. Zobrazí se klíče, pomocí kterých můžete službu Speech Service vyzkoušet po dobu omezeného času.

## <a name="try-the-speech-service-using-a-new-azure-account"></a>Vyzkoušejte službu Speech Service pomocí nového účtu Azure

Pokud si chcete zaregistrovat nový účet Azure, budete potřebovat účet Microsoft. Pokud nemáte účet Microsoft, můžete si na [portálu účet Microsoft](https://account.microsoft.com/account)zdarma zaregistrovat bezplatný poplatek. Vyberte **Přihlásit se účtem Microsoft** a po zobrazení výzvy k přihlášení vyberte **vytvořit účet Microsoft**. Postupujte podle kroků k vytvoření a ověřte svůj nový účet Microsoft.

Jakmile budete mít účet Microsoft, navštivte [stránku registrace do Azure](https://azure.microsoft.com/free/ai/), vyberte **začít zdarma**a vytvořte nový účet Azure pomocí účet Microsoft.

### <a name="create-a-speech-resource-in-azure"></a>Vytvořit prostředek řeči v Azure

> [!NOTE]
> Neomezený počet předplatných úrovně standard můžete vytvořit v jedné nebo víc oblastech. Můžete však vytvořit jenom jedno předplatné bezplatné úrovně. Nasazení modelů na bezplatné úrovni, která zůstanou nevyužitá po dobu 7 dní, se automaticky vyřadí z provozu.

Chcete-li přidat prostředek služby řeči (bezplatné nebo placené úrovně) ke svému účtu Azure:

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) pomocí účet Microsoft.

1. V levém horním rohu portálu vyberte **vytvořit prostředek** . Pokud se vám nepovede **vytvořit prostředek**, můžete ho vždycky najít tak, že v levém horním rohu vyberete sbalenou nabídku:

   ![sbalené navigační tlačítko](media/index/collapsed-nav.png)

1. V **novém** okně zadejte do vyhledávacího pole text "Speech" a stiskněte klávesu ENTER.

1. Ve výsledcích hledání vyberte **řeč**.

   ![výsledky hledání řeči](media/index/speech-search.png)

1. Vyberte **vytvořit**a potom:

   - Zadejte jedinečný název nového prostředku. Název umožňuje rozlišit několik předplatných ve stejné službě.
   - Zvolte předplatné Azure, se nový prostředek je přidružený k určení, jak se účtují poplatky.
   - Vyberte [oblast](regions.md) , ve které se prostředek použije.
   - Vyberte cenovou úroveň Free (F0) nebo Place (S0). Pokud chcete získat úplné informace o cenách a kvótách využití pro každou úroveň, vyberte **Zobrazit úplné podrobnosti o cenách**.
   - Vytvořit novou skupinu prostředků pro toto předplatné řeči nebo předplatné přiřadit existující skupinu prostředků. Pomáhají udržovat vaše různých předplatných Azure, které jsou uspořádány skupiny zdrojů.
   - Vyberte **Vytvořit**. Tím přejdete na přehled nasazení a zobrazíte zprávy o průběhu nasazení.

Nasazení nového prostředku řeči chvíli trvá. Po dokončení nasazení vyberte **Přejít k prostředku** a v levém navigačním podokně vyberte **klíče** . zobrazí se klíče předplatného služby Speech. Každé předplatné má dva klíče; ani jeden klíč můžete použít ve vaší aplikaci. Chcete-li rychle zkopírovat nebo vložit klíč do editoru kódu nebo jiné umístění, vyberte tlačítko Kopírovat vedle každého klíče, přepněte okna a vložte obsah schránky do požadovaného umístění.

> [!IMPORTANT]
> Tyto klíče předplatného se používají pro přístup ke službě rozpoznávání rozhraní API. Nesdílejte své klíče. Uložte je bezpečně – například pomocí Azure Key Vault. Tyto klíče doporučujeme také pravidelně obnovovat. K volání rozhraní API je potřeba jenom jeden klíč. Při opětovném generování prvního klíče můžete použít druhý klíč pro pokračování přístupu ke službě.

## <a name="switch-to-a-new-subscription"></a>Přepnout do nového předplatného

Přepínání z jednoho předplatného do jiného, například kdy vyprší platnost bezplatné zkušební verze nebo když publikujete aplikaci, nahraďte oblast a předplatné klíče ve vašem kódu oblast a předplatné klíč nového prostředku Azure.

## <a name="about-regions"></a>O oblastech

- Pokud vaše aplikace používá [sadu Speech SDK](speech-sdk.md), při vytváření konfigurace řeči zadáte kód oblasti, například `westus`.
- Pokud vaše aplikace používá jedno z [rozhraní REST API](rest-apis.md)služby pro rozpoznávání řeči, je tato oblast součástí identifikátoru URI koncového bodu, který používáte při provádění požadavků.
- Klíče vytvořené pro oblasti jsou platné pouze v dané oblasti. Pokus o použití s jinými oblastmi způsobí chyby ověřování.

## <a name="next-steps"></a>Další kroky

Proveďte jeden z našich šablon rychlý start během 10 minut nebo projděte si naše ukázky SDK:

> [!div class="nextstepaction"]
> [Rychlý Start: rozpoznávání řeči C# v](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
> [ukázek sady Speech pro rozpoznávání](speech-sdk.md#get-the-samples) řeči

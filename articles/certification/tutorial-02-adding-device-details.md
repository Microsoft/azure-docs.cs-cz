---
title: Program pro zařízení Certified v Azure – kurz – přidávání podrobností o zařízení
description: Podrobný průvodce přidáním podrobností o zařízení do projektu na portálu zařízení s certifikací Azure
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: tutorial
ms.date: 03/02/2021
ms.custom: template-tutorial
ms.openlocfilehash: f4f3d045a2530fa54d22bec789918454cba80097
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310423"
---
# <a name="tutorial-add-device-details"></a>Kurz: Přidání podrobností o zařízení

Nyní jste vytvořili projekt pro vaše zařízení a Vy jste vše nastavili pro zahájení procesu certifikace. Nejdřív přidejte podrobnosti o vašem zařízení. Budou zahrnovat technické specifikace, které vaši zákazníci budou moct zobrazit v katalogu zařízení s certifikací Azure a informace o marketingu, které se budou používat k nákupu po rozhodnutí.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidání podrobností o zařízení pomocí funkcí komponent a závislostí
> * Nahrajte Úvodní příručku pro vaše zařízení.
> * Zadejte podrobnosti marketingu pro zákazníky, kteří si mají koupit svoje zařízení.
> * Volitelně Identifikujte jakékoli oborové certifikace.

## <a name="prerequisites"></a>Požadavky

* Měli byste být přihlášeni a mít projekt pro vaše zařízení vytvořený na [portálu zařízení s certifikací Azure](https://certify.azure.com). Další informace najdete v tomto [kurzu](tutorial-01-creating-your-project.md).
* Měli byste mít příručku Začínáme pro vaše zařízení ve formátu PDF. Nabízíme řadu šablon Začínáme, které můžete použít, v závislosti na certifikačním programu a preferovaném jazyce. Šablony jsou k dispozici v naší lokalitě Začínáme v [šablonách](https://aka.ms/GSTemplate "Začít šablony") .

## <a name="adding-technical-device-details"></a>Přidání podrobností o technickém zařízení

První část stránky projektu s názvem "vstupní informace o zařízení" vám umožňuje poskytovat informace o základních hardwarových schopnostech zařízení, jako je třeba název zařízení, popis, procesor, operační systém, možnosti připojení, hardwarová rozhraní, oborové protokoly, fyzické rozměry a další. I když je celá řada polí volitelná, bude většina těchto informací zpřístupněna potenciálním zákazníkům v katalogu zařízení s certifikací Azure, pokud se rozhodnete, že vaše zařízení po certifikaci budete publikovat.

1. Kliknutím na `Add` část Podrobnosti o vstupním zařízení na stránce souhrnu projektu otevřete oddíl podrobnosti o zařízení. Pro dokončení se zobrazí pět oddílů.

![Obrázek stránky s podrobnostmi projektu](./media/images/device-details-menu.png)

2. Zkontrolujte informace, které jste předtím zadali při vytváření projektu na `Basics` kartě.
1. Projděte si certifikáty, které pro zařízení používáte, na `Certifications` kartě.
1. Otevřete `Product details` kartu a vyberte alespoň jeden operační systém.
1. Přidejte **alespoň** jednu samostatnou součást, která popisuje vaše zařízení. Další pokyny k použití komponenty můžete zobrazit [zde](how-to-using-the-components-feature.md).
1. Klikněte na `Save` (Další). Pak budete moct upravit zařízení komponenty a přidat pokročilejší podrobnosti.
1. Seznam dalších podrobností o zařízení, které nejsou zachyceny podrobnostmi o komponentě `Additional product details` .
1. Pokud jste označili `Other` některé z polí komponenty nebo máte zvláštní situaci, kterou byste chtěli opatřit pomocí certifikačního týmu Azure, ponechte v oddílu vysvětlení komentáře `Comments for reviewer` .
1. Kartu použijte `Dependencies` k vypsání závislostí, pokud vaše zařízení vyžaduje další hardware nebo služby pro posílání dat do Azure. Další pokyny k výpisu závislostí najdete [tady](how-to-indirectly-connected-devices.md).
1. Jakmile budete spokojeni s informacemi, které jste zadali, můžete použít `Review` kartu pro přehled úplné sady podrobností o zařízení, které jste zadali.
1. Kliknutím `Project summary` v horní části stránky se vraťte na stránku souhrnu.

![Kontrola stránky s podrobnostmi projektu](./media/images/sample-device-details.png)

## <a name="uploading-a-get-started-guide"></a>Nahrává se příručka Začínáme.

Příručka Začínáme je dokument PDF, který zjednodušuje nastavení a konfiguraci a správu vašeho produktu. Účelem je zjednodušit zákazníkům připojení a podporu zařízení v Azure pomocí vašeho zařízení. V rámci procesu certifikace vyžadujeme, aby naši **partneři poskytovali příručku Začínáme pro** svůj relevantní certifikační program.

1. Prohlédněte si, že jste v závislosti na zadaných [šablonách](https://aka.ms/GSTemplate)zadali všechny požadované informace v dokumentu Začínáme s příručkou Začínáme. Šablonu, kterou použijete, by měla být určena označením certifikačního znaku, pro který se použijete. (Například zařízení IoT technologie Plug and Play bude používat šablonu technologie Plug and Play IoT. Zařízení, která se použijí *jenom* pro certifikaci na úrovni zařízení s certifikací Azure, budou používat šablonu zařízení s certifikací Azure.)
1. Klikněte `Add` v části Průvodce Začínáme stránky souhrnu projektu.

![Obrázek tlačítka GSG](./media/images/gsg-menu.png)

2. Kliknutím na vybrat soubor Nahrajte svůj PDF.
1. Pro formátování si projděte dokument ve verzi Preview.
1. Uložte nahrávání kliknutím na tlačítko Uložit.
1. Kliknutím `Project summary` v horní části stránky se vraťte na stránku souhrnu.

## <a name="providing-marketing-details"></a>Poskytnutí podrobností o marketingu

V této oblasti poskytnete pro svoje zařízení marketingové informace připravené pro zákazníky. Tato pole se zobrazí v katalogu zařízení s certifikací Azure, pokud se rozhodnete pro publikování certifikovaného zařízení.

1. Kliknutím `Add` v části přidat informace o marketingu otevřete stránku podrobnosti o marketingu.

![Obrázek části Podrobnosti o marketingu](./media/images/marketing-details.png)

1. Nahrajte fotografii v produktu ve formátu JPEG nebo PNG, který se použije v katalogu.
1. Napište krátký popis zařízení, který se zobrazí na stránce s popisem produktu v katalogu.
1. Označuje geografickou dostupnost vašeho zařízení.
1. Zadejte odkaz na marketingovou stránku výrobce pro toto zařízení. Mělo by se jednat o odkaz na lokalitu, která poskytuje další informace o zařízení.
    > [!Note]
    > Ujistěte se prosím, že jsou všechny zadané adresy URL platné, nebo budou aktivní v době publikování po schválení. *)

1. Uveďte až 3 cílové podniky, pro které je vaše zařízení optimalizované.
1. Poskytněte informace až pro 5 distributorů vašeho zařízení. To může zahrnovat vlastní web výrobce.

    > [!Note]
    > Pokud není k dispozici adresa URL stránky distributora, `Shop` bude na tlačítku v katalogu výchozí odkaz `Distributor page` , který je pro zařízení určený. V ideálním případě by adresa URL distributora měla vést ke konkrétní stránce, kde zákazník může koupit zařízení, ale není povinný. Pokud je distributor stejný jako výrobce, tato adresa URL může být stejná jako marketingová stránka výrobce. *)

1. Kliknutím `Save` potvrďte své informace.
1. Kliknutím `Project summary` v horní části stránky se vraťte na stránku souhrnu.

## <a name="declaring-additional-industry-certifications"></a>Deklarace dalších certifikátů v oboru

Můžete také povýšit další oborové certifikace, které jste pro svoje zařízení dostali. Tyto certifikace můžou přispět k větší srozumitelnosti zamýšleného použití vašeho zařízení a budou prohledávatelné v katalogu zařízení s certifikací Azure.

1. Klikněte na `Add` část "poskytněte certifikaci v odvětvích".
1. Kliknutím `Add a certification` můžete vybrat ze seznamu běžných certifikačních programů v oboru. Pokud se v tomto produktu dosáhlo certifikace, které není v našem seznamu, můžete zadat vlastní hodnotu řetězce výběrem `Other (please specify)` .
1. Volitelně můžete zadat popis nebo poznámky pro kontrolora. Tyto poznámky ale nebudou veřejně dostupné, aby je bylo možné zobrazit v katalogu.
1. Kliknutím `Save` potvrďte své informace.
1. Kliknutím `Project summary` v horní části stránky se vraťte na stránku souhrnu.

## <a name="next-steps"></a>Další kroky

Nyní jste dokončili proces popisující vaše zařízení. To pomůže týmu pro kontrolu zařízení certifikovaným v Azure a vašemu zákazníkovi lépe porozumět vašemu produktu. Jakmile budete s informacemi, které jste zadali, spokojeni, jste teď připraveni přejít ke fázi testování v rámci procesu certifikace.
> [!div class="nextstepaction"]
> [Kurz: testování zařízení](tutorial-03-testing-your-device.md)

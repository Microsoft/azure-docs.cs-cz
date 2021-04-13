---
title: Jak používat součást komponent na portálu zařízení s certifikací Azure
description: Návod, jak nejlépe využít funkci komponent v části Podrobnosti o zařízení, abyste mohli přesně popsat vaše zařízení
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: how-to
ms.date: 03/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 220a6c2107063734201064115898611c20cab650
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304456"
---
# <a name="add-components-on-the-portal"></a>Přidat komponenty na portál

Při dokončení [kurzu přidávání podrobností o zařízení](tutorial-02-adding-device-details.md) do vašeho certifikačního projektu se očekává, že budete popsáni specifikace hardwaru vašeho zařízení. V takovém případě mohou uživatelé zvýrazňovat více různých hardwarových produktů (označovaných jako **komponenty**), které tvoří vaše zařízení. To vám umožní lépe zvýšit úroveň zařízení, která se dodávají s dalším hardwarem, a umožní zákazníkům najít správný produkt vyhledáním katalogu na základě těchto funkcí.

## <a name="prerequisites"></a>Požadavky

- Měli byste být přihlášeni a mít projekt pro vaše zařízení vytvořený na [portálu zařízení s certifikací Azure](https://certify.azure.com). Další informace najdete v tomto [kurzu](tutorial-01-creating-your-project.md).

## <a name="how-to-add-components"></a>Postup přidání součástí

Každý projekt odeslaný k certifikaci bude obsahovat jednu komponentu **produktu připravenou pro zákazníky** (což v mnoha případech bude představovat samotný produkt holistický). Chcete-li lépe pochopit rozlišení typu komponenty produktu připraveného pro zákazníky, Prohlédněte si náš [certifikační Glosář](./resources-glossary.md). Všechny další součásti jsou podle vašeho uvážení, aby bylo možné zařízení zaznamenat přesně.

1. `Add a component`Na kartě Podrobnosti o produktu vyberte.

    ![Přidat odkaz na součást](./media/images/add-a-component-link.png)

1. Vyplňte příslušná pole formuláře pro komponentu.

    ![Část podrobností komponenty](./media/images/component-details-section.png)

1. Pomocí `Save Product Details` tlačítka v dolní části stránky uložte vaše informace:  

    ![Tlačítko Uložit podrobnosti o produktu](./media/images/save-product-details-button.png)

1. Po uložení komponenty můžete lépe přizpůsobit hardwarové možnosti, které podporuje. Vyberte `Edit` odkaz podle názvu součásti.  

    ![Tlačítko upravit komponentu](./media/images/component-edit.png)

1. V případě potřeby zadejte relevantní informace o schopnostech hardwaru.  

    ![Obrázek oddílů s upravitelnými součástmi](./media/images/component-selection-area.png)  

    Upravitelná pole součásti (zobrazené výše) zahrnují:

    - **Obecné**: podrobnosti o hardwaru, jako jsou například procesory a zabezpečený hardware
    - **Připojení**: možnosti připojení, protokoly a rozhraní, jako jsou přepínače a GPIO
    - **Akcelerátory**: Určete hardwarovou akceleraci, jako je například GPU a VPU
    - **Senzory**: určení dostupných senzorů, jako jsou GPS a vibrace
    - **Další specifikace**: Další informace o zařízení, jako jsou například fyzické rozměry a informace o úložišti/baterii

1. `Save Product Details`V dolní části stránky s podrobnostmi o produktu vyberte.

## <a name="component-use-requirements-and-recommendations"></a>Požadavky a doporučení pro použití komponent

Můžete mít dotazy týkající se množství součástí, které chcete zahrnout, nebo jaký typ komponenty chcete použít. Níže jsou uvedeny příklady několika ukázkových scénářů zařízení, která může být certifikována, a jak můžete použít funkci součásti.

| Typ produktu                                       | No. Komponenty | Součást 1/typ přílohy      | Komponenty 2 +/typ přílohy                    |
|----------------------------------------------------|------------|----------------------------------|--------------------------------------------------|
| Dokončený produkt                                   | 1          | Produkt připravený pro zákazníka, diskrétní | –                                              |
| Dokončený produkt pomocí **odpojených periferních zařízení** | 2 nebo více  | Produkt připravený pro zákazníka, diskrétní | Periferní/diskrétní nebo integrovaná              |
| Dokončený produkt s **integrovanými komponentami**  | 2 nebo více  | Produkt připravený pro zákazníka, diskrétní | Vyberte odpovídající typ/diskrétní nebo integrované. |
| Sada Solution-Ready dev Kit                             | 2 nebo více  | Produkt připravený pro zákazníka, diskrétní | Vyberte odpovídající typ/diskrétní nebo integrované. |

## <a name="example-component-usage"></a>Příklad použití komponenty

Níže jsou uvedené příklady toho, jak by výrobce OEM s názvem contoso používal funkci komponenty k certifikaci svého produktu s názvem Falcon.

1. Falcon je kompletní samostatné zařízení, které se Neintegruje do většího produktu.
    1. No. komponent: 1
    1. Typ zařízení součásti: produkt připravený pro zákazníky
    1. Typ přílohy: diskrétní

     ![Obrázek produktu připraveného pro zákazníky](./media/images/customer-ready-product.png)

1. Falcon je zařízení, které obsahuje integrovaný modul periferních fotoaparátů, který vyrobila společnost INC Electronics, která se připojuje přes USB k Falcon.
    1. No. komponent: 2
    1. Součást typ zařízení: produkt připravený pro zákazníky, periferní zařízení
    1. Typ přílohy: diskrétní, integrovaná
    
    > [!Note]
    > Periferní komponenta je považována za integrovanou, protože není vyměnitelná.

     ![Obrázek součásti ukázkového periferního zařízení](./media/images/peripheral.png)

1. Falcon je zařízení, které obsahuje integrovaný systém v modulu od INC Elektronik, který používá integrovaný procesor Apollo52 od společnosti Espressif a má architekturu ARM64.
    1. No. komponent: 2
    1. Typ zařízení součásti: produkt připravený pro zákazníka, systém v modulu
    1. Typ přílohy: diskrétní, integrovaná

    > [!Note]
    > Periferní komponenta je považována za integrovanou, protože není vyměnitelná. Komponenta SoM by také obsahovala informace o procesoru.

     ![Obrázek ukázkové součásti systému v modulu ](./media/images/system-on-module.png)

## <a name="additional-tips"></a>Další tipy

Nabízíme vám další vysvětlení týkající se zásad používání komponent. Pokud máte nějaké dotazy týkající se vhodného použití komponenty, obraťte se na náš tým na adrese [iotcert@microsoft.com](mailto:iotcert@microsoft.com) a my vám pomůžeme.

1. Projekt musí obsahovat **jenom** jednu komponentu produktu připravenou pro zákazníky. Pokud se rozhodnete pro projekt, který má dvě nezávislá zařízení, musí být tato zařízení certifikována samostatně.
1. K tomu, aby vaše zařízení podporovala potenciální zákazníky, je primárně možné použít (nebo nepoužívat) komponenty.
1. Během prohlížení vašeho zařízení bude certifikační tým Azure vyžadovat, aby se v seznamu vyzobrazovala aspoň jedna součást produktu připravená pro zákazníky. Nicméně můžeme požádat o úpravy informací o komponentách, pokud nejsou jasné nebo se jeví, že nejsou k dispozici (například výrobce komponent není pro typ produktu připravený pro zákazníka).

## <a name="next-steps"></a>Další kroky

Teď, když jste připraveni používat naše komponenty, teď můžete začít s podrobnostmi o svém zařízení, nebo upravit projekt, abyste mohli lépe vyjasnit.

- [Kurz: Přidání podrobností o zařízení](tutorial-02-adding-device-details.md)
- [Úprava publikovaného zařízení](how-to-edit-published-device.md)


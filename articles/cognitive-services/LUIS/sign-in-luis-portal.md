---
title: Přihlášení k portálu LUIS
description: Pokud jste nový uživatel, který se přihlašuje k portálu LUIS, prostředí přihlašování se mírně liší v závislosti na vašem aktuálním uživatelském účtu.
services: cognitive-services
ms.custom: ''
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 09/08/2020
ms.topic: how-to
ms.author: nitinme
author: nitinme
ms.openlocfilehash: ae51dca466a9aaf489ba4628e13a5e13de25b9bc
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96546858"
---
# <a name="sign-in-to-luis-portal"></a>Přihlášení k portálu LUIS

Pokud jste nový uživatel, který se přihlašuje k portálu LUIS, přihlašování se mírně liší v závislosti na vašem aktuálním uživatelském účtu:
  * Přidruženo k předplatnému Azure
  * Nepřidruženo k předplatnému Azure

## <a name="determine-account-type"></a>Určení typu účtu

Když se poprvé přihlásíte na portál LUIS, určete typ účtu pomocí následujících vizuálních indikátorů.

### <a name="account-without-azure-subscription"></a>Účet bez předplatného Azure

Účet, který není přidružený k předplatnému Azure, má ikonu Azure v pravém horním navigačním panelu. Po dokončení migrace na přiřazený typ účtu se ikona přestane zobrazovat.

:::image type="content" source="media/sign-in/sign-in-with-account-without-azure-subscription.png" alt-text="Částečný snímek obrazovky s LUIS navigačním panelem pomocí ikony Azure":::

### <a name="account-with-azure-subscription"></a>Účet s předplatným Azure

Účet přidružený k předplatnému Azure vám umožňuje vybrat předplatné a prostředek, který chcete použít.

:::image type="content" source="media/sign-in/resource-selection.png" alt-text="Částečný snímek obrazovky portálu LUIS s rozevíracími seznamy pro výběr předplatného a vytváření prostředků":::

## <a name="sign-in-with-account-associated-with-an-azure-subscription"></a>Přihlášení pomocí účtu přidruženého k předplatnému Azure

1. Přihlaste se k [portálu Luis](https://www.luis.ai) a odsouhlaste podmínky použití.

1. Přihlásíte se dvě možnosti:

    * Pokračujte v používání prostředku Azure, což je doporučená cesta a brzy bude jediná dostupná cesta. Tato cesta vám umožní propojit účet LUIS s prostředkem pro vytváření prostředků Azure při registraci buď výběrem existujícího prostředku v předplatném, nebo vytvořením nového prostředku. To je ekvivalentní k tomu, že se registrace migruje bez nutnosti pozdějšího [procesu migrace](luis-migration-authoring.md#what-is-migration) . Všichni uživatelé budou muset migrovat do 2. listopadu 2020.

    * Pokračujte v používání počátečního nebo zkušebního klíče. Tato cesta vám umožní přihlásit se k LUIS pomocí počátečního nebo zkušebního prostředku, který je k dispozici bez nutnosti vytvářet žádné prostředky. Pokud zvolíte tuto cestu, budete nakonec muset [migrovat svůj účet](luis-migration-authoring.md#migration-steps) a propojit své aplikace s prostředky pro vytváření obsahu. Proto doporučujeme, abyste zvolili cestu, pro kterou budete pokračovat v prostředku Azure.

    [Přečtěte si další informace o vytváření a počátečních klíčích](luis-how-to-azure-subscription.md#luis-resources). Oba prostředky poskytují 1 000 000 bezplatné transakce vytváření a 1000 bezplatné transakce koncového bodu předpovědi.

    :::image type="content" source="media/sign-in/signup-landing-page.png" alt-text="Částečný snímek obrazovky pro výběr typu Language Understanding prostředek pro vytváření obsahu.":::

1. Použít existující prostředek pro vytváření obsahu

    :::image type="content" source="media/sign-in/signup-choose-resource.png" alt-text="Zvolit prostředek pro vytváření":::

    Pokud už máte LUIS vytváření prostředků ve vašem předplatném a přiřadíte ho k účtu LUIS během přihlašování, vyberte možnost **použít existující prostředek pro vytváření obsahu** a zadejte následující informace:

    * **Tenant** – tenant, ke kterému je předplatné Azure přidružené. Nebudete moci přepnout klienty ze stávajícího okna. Klienty můžete přepínat výběrem miniatury vpravo, která obsahuje vaše iniciály na horním panelu.
    * **Název předplatného** – předplatné, které bude přidruženo k prostředku. Pokud máte více než jedno předplatné, které patří do vašeho tenanta, vyberte v rozevíracím seznamu požadovanou položku.
    * **Název prostředku** – prostředek pro vytváření, ke kterému chcete přidružit účet.

    > [!Note]
    > Pokud je v rozevíracím seznamu zobrazený prostředek pro vytváření obsahu šedě, znamená to, že jste přihlášeni na jiný místní portál. [Pochopení konceptu místních portálů](luis-reference-regions.md#luis-authoring-regions).

1. Vytvořit nový prostředek pro vytváření

    :::image type="content" source="media/sign-in/signup-create-resource.png" alt-text="Vytvořit prostředek pro vytváření":::

    Při **vytváření nového prostředku pro vytváření obsahu** zadejte následující informace:

    * **Tenant** – tenant, ke kterému je předplatné Azure přidružené. Nebudete moci přepnout klienty ze stávajícího okna. Klienty můžete přepínat výběrem miniatury vpravo, která obsahuje vaše iniciály na horním panelu.
    * **Název prostředku** – vlastní název, který zvolíte, se používá jako součást adresy URL pro vytváření vašich transakcí. Název prostředku může obsahovat jenom alfanumerické znaky, '-' a nemůže začínat ani končit znakem '-'. Pokud název obsahuje nějaké jiné symboly, vytvoření prostředku se nezdaří.
    * **Název předplatného** – předplatné, které bude přidruženo k prostředku. Pokud máte více než jedno předplatné, které patří do vašeho tenanta, vyberte v rozevíracím seznamu požadovanou položku.
    * **Skupina prostředků** – název vlastní skupiny prostředků, kterou jste si zvolili v rámci svého předplatného. Skupiny prostředků umožňují seskupit prostředky Azure pro přístup a správu. Pokud v tomto předplatném nemáte skupinu prostředků, nebudete ji moct vytvořit na portálu LUIS. Přejděte na [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.ResourceGroup) a vytvořte si ho tak, že přejdete na Luis a budete pokračovat v procesu přihlašování.

1. Po zvolení cesty může trvat několik sekund, než se zobrazí znaménko "váš účet se úspěšně migrovali". Dokončete výběrem možnosti **pokračovat**.

    :::image type="content" source="media/sign-in/signup-confirm-2.png" alt-text="Potvrdit vytváření prostředku":::

    > [!Note]
    > Pokud máte předplatné a alespoň jeden prostředek pro vytváření v této oblasti, který je stejný jako ten, který se na portále přihlašujete, můžete se automaticky přihlásit k LUIS migrováním a přidruženým k prostředku, aniž byste museli zvolit cestu, na kterou se chcete dostat.


## <a name="sign-in-with-user-account-not-associated-with-an-azure-subscription"></a>Přihlaste se pomocí uživatelského účtu, který není přidružený k předplatnému Azure.

1. Přihlaste se k [portálu Luis](https://www.luis.ai) a ověřte, že souhlasíte s podmínkami použití.

1. Dokončete výběrem možnosti **pokračovat**. Automaticky se přihlásíte pomocí zkušební verze nebo počátečního klíče. To znamená, že nakonec budete muset [migrovat svůj účet](luis-migration-authoring.md#migration-steps) a propojit své aplikace s prostředky pro vytváření obsahu. Aby se proces migrace mohl absolvovat, musíte se přihlásit k [bezplatné zkušební verzi Azure](https://azure.microsoft.com/free/).

    :::image type="content" source="media/sign-in/signup-no-subscription.png" alt-text="Žádný scénář předplatného":::

## <a name="troubleshooting"></a>Poradce při potížích

* Pokud vytvoříte prostředek pro vytváření obsahu z Azure Portal v jiné oblasti než na portálu, ke kterému jste se přihlásili, bude zdroj pro vytváření šedý.
* Při vytváření nového prostředku se ujistěte, že název prostředku obsahuje pouze alfanumerické znaky, '-' a nemůže začínat ani končit znakem '-'. V opačném případě selže.
* Ujistěte se, že máte [ve svém předplatném správná oprávnění k vytvoření prostředku Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles). Pokud nemáte příslušná oprávnění, obraťte se na správce předplatného, abyste vám poskytli dostatečná oprávnění.

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak [spustit novou aplikaci](luis-how-to-start-new-app.md)

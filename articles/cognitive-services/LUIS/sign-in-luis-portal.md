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
ms.openlocfilehash: 8a4b86fb8fa5a87283178d957e07617b908e6286
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2021
ms.locfileid: "98790897"
---
# <a name="sign-in-to-luis-portal"></a>Přihlášení k portálu LUIS

[!INCLUDE [LUIS Free account](includes/luis-portal-note.md)]

Pomocí tohoto článku můžete začít s portálem LUIS a vytvořit prostředek pro vytváření obsahu. Po dokončení kroků v tomto článku budete moct vytvářet a publikovat aplikace pro LUIS.

## <a name="access-the-portal"></a>Přístup k portálu


1. Pokud chcete začít pracovat s LUIS, přečtěte si [portál Luis](https://www.luis.ai). Pokud ještě nemáte předplatné, zobrazí se výzva, abyste si vytvořili [bezplatný účet](https://azure.microsoft.com//free/cognitive-services/) a vrátili se zpátky na portál.
2. Aktualizujte stránku, aby se aktualizovala s nově vytvořeným předplatným.
3. Vyberte své předplatné z rozevíracího seznamu.

    > [!div class="mx-imgBorder"]
    > ![vybrat odběry](./media/migrate-authoring-key/select-subscription-sign-in-2.png)

4. Pokud vaše předplatné bydlí v rámci jiného tenanta, nebudete moci přepnout klienty ze stávajícího okna. Klienty můžete přepnout tak, že toto okno zavřete a v horním panelu vyberete miniaturu, která obsahuje vaše iniciály. Kliknutím na **vybrat jiný prostředek pro vytváření obsahu** v horní části otevřete okno znovu.

    > [!div class="mx-imgBorder"]
    > ![Přepnout adresáře](./media/migrate-authoring-key/switch-directories.png)

5. Pokud máte k vašemu předplatnému přidružený prostředek pro vytváření LUIS, vyberte ho z rozevíracího seznamu. Můžete zobrazit všechny aplikace, které jsou vytvořeny v rámci tohoto prostředku pro vytváření.
6. Pokud ne, klikněte na **vytvořit nový prostředek pro vytváření** ve spodní části tohoto modálního programu.
7.  Při vytváření nového prostředku pro vytváření obsahu zadejte následující informace:

    > [!div class="mx-imgBorder"]
    > ![Vytvořit nový prostředek](./media/migrate-authoring-key/create-new-authoring-resource-2.png)

    * **Název tenanta** – tenant, ke kterému je předplatné Azure přidružené. Nebudete moci přepnout klienty ze stávajícího okna. Klienty můžete přepnout tak, že toto okno zavřete a v pravém horním rohu obrazovky vyberete miniaturu, která obsahuje vaše iniciály. Vyberte vybrat **jiný prostředek pro vytváření obsahu** v horní části a znovu otevřete okno.
    * **Název skupiny prostředků Azure** – název vlastní skupiny prostředků, kterou jste si zvolili v rámci svého předplatného. Skupiny prostředků umožňují seskupit prostředky Azure pro přístup a správu. Pokud v tomto předplatném nemáte skupinu prostředků, nebudete ji moct vytvořit na portálu LUIS. Přejděte na [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.ResourceGroup) a vytvořte si ho tak, že přejdete na Luis a budete pokračovat v procesu přihlašování.
    * **Název prostředku Azure** – vlastní název, který zvolíte, se používá jako součást adresy URL pro vytváření vašich transakcí. Název prostředku může obsahovat jenom alfanumerické znaky, `-` a nemůže začínat ani končit `-` . Pokud název obsahuje nějaké jiné symboly, vytvoření prostředku se nezdaří.
    * **Umístění** – volba pro vytváření aplikací v jednom ze [tří umístění pro vytváření obsahu](./luis-reference-regions.md) , která jsou aktuálně podporovaná Luis, včetně: západní USA, západní Evropa a východní Austrálie
    * **Cenová úroveň** – ve výchozím nastavení je cenová úroveň vytváření F0 vybraná, protože to je doporučené. Pokud hledáte další vrstvu zabezpečení, vytvořte si [klíč spravovaný zákazníkem](./luis-encryption-of-data-at-rest.md#customer-managed-keys-for-language-understanding) z Azure Portal.
8. Teď jste se úspěšně přihlásili k LUIS. Nyní můžete začít vytvářet aplikace.

## <a name="troubleshooting"></a>Odstraňování potíží

* Při vytváření nového prostředku se ujistěte, že název prostředku obsahuje pouze alfanumerické znaky, '-' a nemůže začínat ani končit znakem '-'. V opačném případě selže.
* Ujistěte se, že máte [ve svém předplatném správná oprávnění k vytvoření prostředku Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles). Pokud nemáte příslušná oprávnění, obraťte se na správce předplatného, abyste vám poskytli dostatečná oprávnění.

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak [spustit novou aplikaci](luis-how-to-start-new-app.md)
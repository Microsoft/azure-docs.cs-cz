---
title: 'Rychlý Start: vytvoření účtu Azure dosah v Azure Portal (Preview)'
description: V tomto rychlém startu se dozvíte, jak vytvořit účet Azure dosah a nakonfigurovat oprávnění, abyste ho mohli začít používat.
author: nayenama
ms.author: nayenama
ms.date: 10/23/2020
ms.topic: quickstart
ms.service: purview
ms.subservice: purview-data-catalog
ms.custom:
- mode-portal
ms.openlocfilehash: 72f4ac8df39b9511fd98a1dd5a3eca76e11e34bf
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535159"
---
# <a name="quickstart-create-an-azure-purview-account-in-the-azure-portal"></a>Rychlý Start: vytvoření účtu Azure dosah v Azure Portal

> [!IMPORTANT]
> Služba Azure dosah je aktuálně ve verzi PREVIEW. [Doplňkové podmínky použití pro](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) verze Preview Microsoft Azure zahrnují další právní podmínky, které se vztahují na funkce Azure, které jsou ve verzi beta, Preview nebo jinak ještě nedostupné ve všeobecné dostupnosti.

V tomto rychlém startu vytvoříte účet Azure dosah.

## <a name="prerequisites"></a>Požadavky

* Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Vlastní [klient Azure Active Directory](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

* Váš účet musí mít oprávnění k vytváření prostředků v předplatném.

* Pokud jste **Azure Policy** blokující všechny aplikace při vytváření **účtu úložiště** a **oboru názvů EventHub**, je potřeba vytvořit výjimku zásad pomocí značky, kterou je možné zadat během procesu vytváření účtu dosah. Hlavním důvodem je to, že pro každý vytvořený účet dosah je potřeba vytvořit spravovanou skupinu prostředků a v rámci této skupiny prostředků, účtu úložiště a oboru názvů EventHub.

    > [!important]
    > Nemusíte postupovat podle tohoto kroku, pokud nemáte Azure Policy nebo existující Azure Policy neblokuje vytváření **účtu úložiště** a **oboru názvů EventHub**.

    1. Přejděte na Azure Portal a vyhledejte **zásady** .
    1. Postupujte podle pokynů pro [Vytvoření vlastní definice zásad](../governance/policy/tutorials/create-custom-policy-definition.md) nebo upravte existující zásady a přidejte dvě výjimky s `not` operátorem a `resourceBypass` tagem:

        ```json
        {
          "mode": "All",
          "policyRule": {
            "if": {
              "anyOf": [
              {
                "allOf": [
                {
                  "field": "type",
                  "equals": "Microsoft.Storage/storageAccounts"
                },
                {
                  "not": {
                    "field": "tags['<resourceBypass>']",
                    "exists": true
                  }
                }]
              },
              {
                "allOf": [
                {
                  "field": "type",
                  "equals": "Microsoft.EventHub/namespaces"
                },
                {
                  "not": {
                    "field": "tags['<resourceBypass>']",
                    "exists": true
                  }
                }]
              }]
            },
            "then": {
              "effect": "deny"
            }
          },
          "parameters": {}
        }
        ```
        
        > [!Note]
        > Tato značka může být vedle sebe a je až na to, `resourceBypass` abyste mohli definovat hodnotu při vytváření dosah v těchto krocích, pokud zásada může detekovat značku.

        :::image type="content" source="./media/create-catalog-portal/policy-definition.png" alt-text="Snímek obrazovky ukazující, jak vytvořit definici zásady":::

    1. [Vytvořte přiřazení zásady](../governance/policy/assign-policy-portal.md) pomocí vytvořených vlastních zásad.

        [![Snímek obrazovky ukazující, jak vytvořit přiřazení zásad](./media/create-catalog-portal/policy-assignment.png)](./media/create-catalog-portal/policy-assignment.png#lightbox)

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí svého účtu Azure.

## <a name="configure-your-subscription"></a>Konfigurace předplatného

V případě potřeby pomocí těchto kroků nakonfigurujte předplatné, aby bylo možné Azure dosah spustit ve vašem předplatném:

   1. V Azure Portal vyhledejte a vyberte **předplatná**.

   1. V seznamu předplatných vyberte předplatné, které chcete použít. Vyžaduje se oprávnění k přístupu pro správu předplatného.

      :::image type="content" source="./media/create-catalog-portal/select-subscription.png" alt-text="Snímek obrazovky ukazující, jak vybrat odběr v Azure Portal.":::

   1. U svého předplatného vyberte **poskytovatelé prostředků**. V podokně **poskytovatelé prostředků** vyhledejte a zaregistrujte všechny tři poskytovatele prostředků: 
       1. **Microsoft. dosah**
       1. **Microsoft.Storage**
       1. **Microsoft. EventHub** 
      
      Pokud nejsou registrovány, zaregistrujte je výběrem možnosti **Registrovat**.

      :::image type="content" source="./media/create-catalog-portal/register-purview-resource-provider.png" alt-text="Snímek obrazovky, který ukazuje, jak zaregistrovat poskytovatele prostředků Microsoft dosahe Azure v Azure Portal.":::

## <a name="create-an-azure-purview-account-instance"></a>Vytvoření instance účtu Azure dosah

1. Na Azure Portal na stránce **účty dosah** a pak vyberte **Přidat** a vytvořte nový účet Azure dosah. Případně můžete přejít na web Marketplace Search pro **účty dosah** a vybrat **vytvořit**. Všimněte si, že můžete současně přidat jenom jeden účet Azure dosah.

   :::image type="content" source="./media/create-catalog-portal/add-purview-instance.png" alt-text="Snímek obrazovky ukazující, jak vytvořit instanci účtu Azure dosah v Azure Portal.":::

    > [!Note] 
    > Azure dosah nepodporuje přesun svého účtu do různých oblastí. Další informace najdete na [stránce podporované služby Azure](../azure-resource-manager/management/region-move-support.md).

1. Na kartě **základy** proveďte následující:
    1. Vyberte **skupinu prostředků**.
    1. Zadejte **název účtu dosah** pro váš katalog. Mezery a symboly nejsou povoleny.
    1. Zvolte  **umístění** a potom vyberte **Další: Konfigurace**.
1. Na kartě **Konfigurace** vyberte požadovanou **Velikost platformy** – povolené hodnoty jsou 4 jednotky kapacity (CU) a 16 cu. Vyberte **Další: značky**.
1. Na kartě **značky** můžete volitelně přidat jednu nebo více značek. Tyto značky jsou používány pouze v Azure Portal, nikoli v Azure dosah. 

    > [!Note] 
    > Pokud máte **Azure Policy** a potřebujete přidat výjimku jako v **požadavcích**, je nutné přidat správnou značku. Můžete například přidat `resourceBypass` značku: :::image type="content" source="./media/create-catalog-portal/add-purview-tag.png" alt-text="Přidání značky k účtu dosah.":::

1. Vyberte **zkontrolovat & vytvořit** a pak vyberte **vytvořit**. Dokončení vytváření může trvat několik minut. Nově vytvořená instance účtu Azure dosah se zobrazí v seznamu na stránce **účty dosah** .
1. Až se nové zřizování účtů dokončí, vyberte **Přejít k prostředku**.

    > [!Note]
    > Pokud se zřizování nepovedlo se `Conflict` stavem, znamená to, že zásady Azure blokují dosah z vytváření **účtu úložiště** a **oboru názvů EventHub**. Chcete-li přidat výjimky, je nutné projít kroky pro splnění **požadavků** .
    > :::image type="content" source="./media/create-catalog-portal/purview-conflict-error.png" alt-text="Chybová zpráva ke konfliktu dosah":::

1. Vyberte **spustit účet dosah**.

   :::image type="content" source="./media/use-purview-studio/launch-from-portal.png" alt-text="Snímek obrazovky výběru pro spuštění katalogu účtů Azure dosah":::

## <a name="add-a-security-principal-to-a-data-plane-role"></a>Přidání objektu zabezpečení do role roviny dat

Předtím, než budete moci začít používat Azure dosah, musí být jeden nebo více objektů zabezpečení přidáno do jedné z předem definovaných rolí datové roviny: **dosah data Reader**, **dosah data kurátor** nebo **dosah správce zdrojů dat**. Další informace o oprávněních Data Catalog Azure dosah najdete v tématu [oprávnění katalogu](catalog-permissions.md).

Pokud chcete přidat objekt zabezpečení do role **dosah data kurátor** data roviny v účtu Azure dosah:

1. Přejít na stránku [**účty dosah**](https://aka.ms/purviewportal) v Azure Portal.

1. Vyberte účet Azure dosah, který chcete upravit.

1. Na stránce **dosah účet** vyberte možnost **řízení přístupu (IAM)** na kartě.

1. Klikněte na **+ Přidat** .

Pokud kliknete na tlačítko Přidat, zobrazí se dvě možnosti s označením (zakázáno). to znamená, že nemáte správná oprávnění k přidání kohokoli do role roviny dat v účtu Azure dosah. V účtu Azure dosah musíte najít vlastníka, správce přístupu uživatele nebo někoho jiného s oprávněním pro přiřazení role. Správnou osobu můžete vyhledat tak, že vyberete kartu **přiřazení rolí** a pak se posuňte dolů, aby vypadala vlastník nebo správce přístupu uživatelů a kontaktovali tyto osoby.

1. Vyberte **Přidat přiřazení role**.

1. Pro typ role v roli **dosah data kurátor role** nebo **Správce zdrojů dat dosah** v závislosti na tom, jaký objekt zabezpečení se bude používat (podrobnosti najdete v tématu [oprávnění katalogu](catalog-permissions.md) a [objekty zabezpečení aplikací a služeb v Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md) ).

1. Pro **přiřazení přístupu** ponechte výchozí nastavení, **uživatele, skupinu nebo instanční objekt**.

1. V poli **Vyberte** zadejte jméno uživatele, Azure Active Directory skupiny nebo instančního objektu, který chcete přiřadit, a potom v podokně výsledků klikněte na jeho název.

1. Klikněte na **Uložit**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už tento účet Azure dosah nepotřebujete, odstraňte ho pomocí následujících kroků:

1. Přejít na stránku **účty dosah** v Azure Portal.

2. Vyberte účet Azure dosah, který jste vytvořili na začátku tohoto rychlého startu. Vyberte **Odstranit**, zadejte název účtu a pak vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak vytvořit účet Azure dosah.

V dalším článku se dozvíte, jak uživatelům dovolit přístup k vašemu účtu Azure dosah. 

> [!div class="nextstepaction"]
> [Přidání uživatelů k účtu Azure dosah](catalog-permissions.md)

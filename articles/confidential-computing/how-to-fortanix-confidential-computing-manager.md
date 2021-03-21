---
title: Fortanix důvěrného Computing Manageru ve spravované aplikaci Azure
description: Naučte se, jak nasadit Fortanix důvěrného Computing Manager (CCM) ve spravované aplikaci v Azure Portal.
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/03/2021
ms.author: jencook
ms.openlocfilehash: 757ce9b7502316bbc8a5b8f27ba672048b7bbace
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102563417"
---
# <a name="fortanix-confidential-computing-manager-in-an-azure-managed-application"></a>Fortanix důvěrného Computing Manageru ve spravované aplikaci Azure

V tomto článku se dozvíte, jak nasadit aplikaci, která je spravovaná pomocí Fortanix důvěrného Computing Manageru v Azure Portal.

Fortanix je dodavatel softwaru třetí strany s produkty a službami postavenými nad infrastrukturou Azure. Existují další poskytovatelé třetích stran nabízející podobné důvěrné výpočetní služby v Azure.

> [!NOTE]
>Produkty, na které se odkazuje v tomto dokumentu, nejsou pod kontrolou Microsoftu. Společnost Microsoft poskytuje tyto informace jenom jako pohodlí a odkaz na tyto produkty od jiných výrobců nepředpokládá, že Microsoft.

## <a name="prerequisites"></a>Předpoklady

- Soukromý registr Docker pro nabízení převedených imagí aplikace
- Pokud ještě nemáte předplatné Azure, vytvořte si [účet](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) před tím, než začnete.

## <a name="deploy-a-confidential-computing-manager-through-an-azure-managed-application"></a>Nasazení správce důvěrných výpočtů prostřednictvím spravované aplikace Azure

1. Přejděte na [Azure Portal](https://portal.azure.com/).

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/azure-portal.png" alt-text="Azure Portal.":::

2. Na panelu hledání vyhledejte text "Fortanix důvěrného Computing Manager" a najdete seznam Marketplace pro Fortanix CCM. **V Azure vyberte Fortanix důvěrného Computing Manager**.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/search-marketplace-listing.png" alt-text="Výpis Marketplace.":::

3. Otevře se stránka, na které se vytváří aplikace spravovaná CCM. Vyberte **vytvořit**.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/create-managed-application.png" alt-text="Vytvořte aplikaci.":::

4. Vyplňte všechna povinná pole.
   1. V části Podrobnosti spravované aplikace bude v poli **spravovaná skupina prostředků** výchozí hodnota, kterou může uživatel upravovat, pokud to potřebují.
   2. V poli **oblast** vyberte možnost Austrálie – **východ**, **Austrálie – jihovýchod**, **východní USA**, **západní USA 2**, **západní Evropa**, **Severní Evropa**, **Kanada – střed**, Kanada – **východ** nebo **východní USA 2 EUAP**.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/required-fields.png" alt-text="Povinná pole":::

   Výběrem **Zobrazit + vytvořit** vytvořte aplikaci spravovanou pomocí Fortanix ccm.

5. Zkontrolujte podrobnosti a po úspěšném ověření zaškrtněte políčko Souhlasím **s podmínkami a ujednáními** a pak vyberte **vytvořit** a vytvořte spravovanou aplikaci.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/review-details.png" alt-text="Zkontrolujte podrobnosti.":::

6. Nasazení CCM Fortanix se spustí a upozorní, že nasazení probíhá.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/deployment-progress.png" alt-text="Průběh nasazení.":::

7. Po dokončení nasazení vyberte tlačítko **Přejít k prostředku** a přejdete na stránku "Přehled nasazené aplikace spravované pro ccm" a zaregistrujete tak výpočetní uzel.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/fortanix-resource.png" alt-text="Snímek obrazovky, který zobrazuje úspěšné nasazení v Azure Portal.]":::

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/fortanix-overview.png" alt-text="Snímek obrazovky, který zobrazuje přehled prostředku důvěrného výpočetního prostředí v Azure Portal.":::

## <a name="enroll-the-compute-node-in-fortanix-ccm"></a>Registrace výpočetního uzlu v Fortanix CCM

1. V navigační nabídce vlevo vyberte **správce důvěrných computingů** . Přihlaste se k Fortanix CCM a vytvořte účet, jak vidíte na **obrázku 9**.

    Další podrobnosti o tom, jak se zaregistrovat, přihlášení a vytvoření účtu v CCM najdete v tématu [ccm Začínáme](https://support.fortanix.com/hc/en-us/articles/360034373551-User-s-Guide-Logging-in).
    
    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/fortanix-login.png" alt-text="Snímek obrazovky zobrazující přihlášení Fortanix důvěrného Computing Manageru":::
    
2. Pokud chcete získat token JOIN z konzoly pro správu CCM, nejprve vyberte tlačítko **zaregistrovat uzel** . Pak v okně zaregistrovat uzel vyberte tlačítko **Kopírovat** a zkopírujte token JOIN.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/get-join-token.png" alt-text="Snímek obrazovky, který ukazuje získání tokenu JOIN":::

3. Nyní můžete zaregistrovat agenta uzlu, vybrat kartu pro **důvěrné výpočetní** prostředí a vybrat **Přidat** a přidat agenta uzlu ccm.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/add-node-agent.png" alt-text="Snímek obrazovky, který ukazuje přidání agenta uzlu":::

4.  Ve formuláři agenta uzlu CCM vyplňte všechna povinná pole. Vložte token JOIN, který jste zkopírovali v kroku 2 v **tokenu JOIN**. Kliknutím na tlačítko **zkontrolovat a odeslat** potvrďte akci.

    Další informace o tom, jak zaregistrovat výpočetní uzel CCM, najdete v tématu [registrace výpočetního uzlu](https://support.fortanix.com/hc/en-us/articles/360043085652-User-s-Guide-Compute-Nodes).
    
    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/enroll-compute-node.png" alt-text="Snímek obrazovky, který ukazuje registraci výpočetního uzlu":::
    
5. Po úspěšném ověření vyberte **Odeslat** a dokončete vytvoření agenta uzlu.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/node-agent-created.png" alt-text="Snímek obrazovky, který ukazuje, že je vytvořen agent uzlu.":::

6. Chcete-li zjistit stav nasazení, klikněte na kartu **Přehled** a vyberte odkaz **spravované skupiny prostředků** .

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/node-enrolled.png" alt-text="Snímek obrazovky, který ukazuje registraci uzlu.":::
    
    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/managed-resource-group.png" alt-text="Snímek obrazovky, který ukazuje kontrolu stavu nasazení.":::

7. Nyní si všimnete, že stav nasazení stále probíhá, a bude trvat několik minut, než se agent uzlu úspěšně zaregistruje.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/deployment-in-progress.png" alt-text="Snímek obrazovky znázorňující probíhající nasazení":::

8. Po úspěšné registraci agenta uzlu se stav změní na úspěch.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/deployment-succeeded.png" alt-text="Snímek obrazovky, který ukazuje nasazení bylo úspěšné.":::

9. Teď v aplikaci spravované CCM přejdete na stránky výpočetní uzly a zjistíte, že uzel je v **aktivním** stavu a úspěšně se zapsal.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/node-active-state.png" alt-text="Snímek obrazovky, který zobrazuje úspěšné zaregistrování uzlu.":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

Uživatel také může odstranit agenta uzlu CCM ze stránky agenta pro důvěrné výpočetní uzly. Chcete-li odstranit agenta uzlu, vyberte agenta uzlu a vyberte tlačítko **Odstranit** na horním panelu.

:::image type="content" source="media/how-to-fortanix-confidential-computing-manager/delete-node-agent.png" alt-text="Snímek obrazovky, který ukazuje odstranění agenta uzlu.":::

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zaregistrovali uzel pomocí spravované aplikace Azure do správce důvěrného computingu Fortanix. Registrace uzlu umožňuje převést Image aplikace tak, aby běžela nad důvěrným výpočetním virtuálním počítačem. Další informace o důvěrných výpočetních virtuálních počítačích v Azure najdete v tématu [řešení na Virtual Machines](virtual-machine-solutions.md).

Další informace o nabídkách důvěrného výpočetního prostředí Azure najdete v tématu [důvěrné výpočetní prostředí Azure](overview.md).

Naučte se, jak provádět podobné úlohy pomocí dalších nabídek třetích stran v Azure, jako jsou [Anjuna](https://azuremarketplace.microsoft.com/marketplace/apps/anjuna-5229812.aee-az-v1) a [Scone](https://sconedocs.github.io).


---
title: Transformace XML pomocí XSLT map – Azure Logic Apps | Dokumentace Microsoftu
description: Přidaní map XSLT, které transformují XML v Azure Logic Apps sadou Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 90f5cfc4-46b2-4ef7-8ac4-486bb0e3f289
ms.date: 07/08/2016
ms.openlocfilehash: c5e5e0a0a3f8bd5feedc00d5bbfb76a1453ccc84
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2018
ms.locfileid: "43123552"
---
# <a name="add-maps-for-xml-transformation-in-azure-logic-apps-with-enterprise-integration-pack"></a>Přidání map pro transformaci XML v Azure Logic Apps sadou Enterprise Integration Pack

Podniková integrace používá mapy pro transformaci dat XML mezi formáty. Dokument XML, který definuje data v dokumentu, který by měl transformována do jiného formátu je objekt map. 

## <a name="why-use-maps"></a>Proč používat mapování?

Předpokládejme, že pravidelně dostanete B2B objednávky nebo faktury od zákazníka, který používá formát YYYMMDD pro data. Ale ve vaší organizaci, ukládat data ve formátu MMDDYYY. Můžete použít mapu, která bude *transformace* YYYMMDD formát data do MMDDYYY před uložením podrobnosti objednávky nebo faktury v databázi aktivity zákazníků.


## <a name="how-do-i-create-a-map"></a>Jak vytvořit mapu?

Můžete vytvářet projekty integrace BizTalk, se [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "přečtěte si víc o enterprise integration pack") pro Visual Studio 2015. Potom můžete vytvořit soubor integrace mapy, která umožňuje vizuálně mapovat položky mezi dvěma soubory schématu XML. Po vytvoření tohoto projektu, budete mít dokumentu XSLT.

Pokud mapa obsahuje odkaz na externí sestavení, pak oba musí být odeslán do účtu pro integraci. Měly by být odeslány v určitém pořadí, nejprve sestavení a pak mapu, která odkazuje na sestavení.


## <a name="how-do-i-add-a-map"></a>Jak mohu přidat mapu?

1. Na webu Azure Portal, vyberte **Procházet**.

    ![](./media/logic-apps-enterprise-integration-overview/overview-1.png)

2. Do vyhledávacího pole filtru zadejte **integrace**a pak vyberte **účty pro integraci** ze seznamu výsledků.

    ![](./media/logic-apps-enterprise-integration-overview/overview-2.png)

3. Vyberte, ve které chcete přidat mapu účtu integrace.

    ![](./media/logic-apps-enterprise-integration-overview/overview-3.png)

4. Vyberte **mapy** dlaždici.

    ![](./media/logic-apps-enterprise-integration-maps/map-1.png)

5. Až se otevře se stránka map, zvolte **přidat**.

    ![](./media/logic-apps-enterprise-integration-maps/map-2.png)  

6. Zadejte **název** pro mapu. Pokud chcete nahrát soubor mapy, vyberte ikonu složky na pravé straně **mapy** textového pole. Po dokončení procesu nahrávání, zvolte **OK**.

    ![](./media/logic-apps-enterprise-integration-maps/map-3.png)

7. Po Azure přidá k účtu pro integraci na mapě, obdržíte zprávu na obrazovce, který ukazuje, jestli váš soubor mapování byl přidán nebo ne. Až se vám tato zpráva zobrazí, zvolte **mapy** dlaždici, abyste mohli zobrazit nově přidané mapy.

    ![](./media/logic-apps-enterprise-integration-maps/map-4.png)


## <a name="how-do-i-add-an-assembly"></a>Jak mohu přidat sestavení?
Otevřete ve které chcete nahrát sestavení účtu integrace.

1. Zvolte **sestavení** dlaždici.

    ![integrationaccount sestavení dlaždice](./media/logic-apps-enterprise-integration-maps/assemblytile.png)

2. Až se otevře se stránka sestavení, zvolte **přidat**. Zadejte **název** pro vaše sestavení. Pokud chcete nahrát soubor sestavení, zvolte ikonu složky na pravé straně **sestavení** textového pole. Po dokončení procesu nahrávání, zvolte **OK**.

    ![Přidat sestavení](./media/logic-apps-enterprise-integration-maps/assemblyfile.png)


## <a name="how-do-i-edit-a-map"></a>Jak mohu upravit mapu?

Musíte nahrát nový soubor mapy s požadované změny. Nejdřív si můžete stáhnout mapování pro úpravy.

Pokud chcete nahrát novou mapu, která nahradí existující mapování, postupujte takto.

1. Zvolte **mapy** dlaždici.

2. Po otevření stránky mapy vyberte mapu, kterou chcete upravit.

3. Na **mapy** zvolte **aktualizace**.

    ![](./media/logic-apps-enterprise-integration-maps/edit-1.png)

4. Na výběr souborů vyberte soubor mapy, který chcete nahrát a pak vyberte **otevřít**.

    ![](./media/logic-apps-enterprise-integration-maps/edit-2.png)

## <a name="how-to-delete-a-map"></a>Jak odstranit mapování?

1. Zvolte **mapy** dlaždici.

2. Po otevření stránky mapy vyberte mapu, kterou chcete odstranit.

3. Zvolte **odstranit**.

    ![](./media/logic-apps-enterprise-integration-maps/delete.png)

4. Potvrzení odstranění na mapě.

    ![](./media/logic-apps-enterprise-integration-maps/delete-confirmation-1.png)

## <a name="next-steps"></a>Další kroky
* [Další informace o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "přečtěte si víc o Enterprise Integration Pack")  
* [Další informace o smlouvy](../logic-apps/logic-apps-enterprise-integration-agreements.md "přečtěte si víc o smlouvách enterprise integration")  
* [Další informace o transformacích](logic-apps-enterprise-integration-transform.md "přečtěte si víc o transformacích podnikové integrace")  


---
title: Přidání schémat pro ověřování XML – Azure Logic Apps | Dokumentace Microsoftu
description: Vytvoření schémat, které byly ověřeny dokumentů XML v Azure Logic Apps sadou Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 56c5846c-5d8c-4ad4-9652-60b07aa8fc3b
ms.date: 07/29/2016
ms.openlocfilehash: e03346da1c2b77f885c39d5329f990684979c56e
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2018
ms.locfileid: "43123069"
---
# <a name="validate-xml-with-schemas-in-azure-logic-apps-with-enterprise-integration-pack"></a>Ověření XML se schématy v Azure Logic Apps sadou Enterprise Integration Pack

Schémata potvrdit, že dokumentů XML, který jste dostali jsou platná a mají očekávaná data ve formátu předdefinované. Schémata také pomoci ověření zprávy, které se vyměňují ve scénáři B2B.

## <a name="add-a-schema"></a>Přidat schéma

1. Na webu Azure Portal, vyberte **všechny služby**.

    ![Azure portal, "Všechny služby"](media/logic-apps-enterprise-integration-schemas/overview-11.png)

2. Do vyhledávacího pole filtru zadejte **integrace**a vyberte **účty pro integraci** ze seznamu výsledků.

    ![Pole filtru hledání](media/logic-apps-enterprise-integration-schemas/overview-21.png)

3. Vyberte **účtu pro integraci** ve které chcete přidat schéma.

    ![Seznam účtů pro integraci](media/logic-apps-enterprise-integration-schemas/overview-31.png)

4. Zvolte **schémata** dlaždici.

    ![Příklad integrace účtu "Schémata"](media/logic-apps-enterprise-integration-schemas/schema-11.png)

### <a name="add-a-schema-file-smaller-than-2-mb"></a>Přidat soubor schématu, která je menší než 2 MB

1. V **schémata** okno, které se otevře (z předchozích kroků), zvolte **přidat**.

    ![Okno schémata, "Přidat"](media/logic-apps-enterprise-integration-schemas/schema-21.png)

2. Zadejte název vašeho schématu. Nahrát soubor schématu vedle výběrem ikony složky **schématu** pole. Po dokončení procesu nahrávání, vyberte **OK**.

    ![Snímek obrazovky "Přidat schéma", se zvýrazněným "malý soubor"](media/logic-apps-enterprise-integration-schemas/schema-31.png)

### <a name="add-a-schema-file-larger-than-2-mb-up-to-8-mb-maximum"></a>Přidat soubor schématu, která je větší než 2 MB (nejvýše 8 MB)

Tyto kroky se liší v závislosti na úrovni objektů blob v kontejneru přístup: **veřejné** nebo **bez anonymního přístupu**.

**Chcete-li zjistit tato úroveň přístupu**

1.  Otevřít **Průzkumníka služby Azure Storage**. 

2.  V části **kontejnery objektů Blob**, vyberte kontejner objektů blob, který chcete. 

3.  Vyberte **zabezpečení**, **úroveň přístupu**.

Pokud je úroveň přístupu objektu blob zabezpečení **veřejné**, postupujte podle těchto kroků.

![Průzkumník služby Azure Storage, pomocí "Kontejnery objektů Blob", "Zabezpečení" a "Public" zvýrazněnou](media/logic-apps-enterprise-integration-schemas/blob-public.png)

1. Nahrajte schéma do účtu úložiště a zkopírujte identifikátor URI.

    ![Účet úložiště, se zvýrazněným identifikátorem URI](media/logic-apps-enterprise-integration-schemas/schema-blob.png)

2. V **přidat schéma**vyberte **velkých souborů**a zadejte identifikátor URI v **identifikátor URI obsahu** textového pole.

    ![Schémata se tlačítko "Přidat" a "velký" zvýrazněnou](media/logic-apps-enterprise-integration-schemas/schema-largefile.png)

Pokud je úroveň přístupu objektu blob zabezpečení **bez anonymního přístupu**, postupujte podle těchto kroků.

![Průzkumník služby Azure Storage, se "Kontejnery objektů Blob", "Zabezpečení" a "Bez anonymního přístupu" zvýrazněnou](media/logic-apps-enterprise-integration-schemas/blob-1.png)

1. Nahrajte schéma do účtu úložiště.

    ![Účet úložiště](media/logic-apps-enterprise-integration-schemas/blob-3.png)

2. Vygenerování sdíleného přístupového podpisu pro schéma.

    ![Účet úložiště se zvýrazněnou kartu podpisy sdíleného přístupu](media/logic-apps-enterprise-integration-schemas/blob-2.png)

3. V **přidat schéma**vyberte **velkých souborů**a zadejte URI sdíleného přístupového podpisu v **identifikátor URI obsahu** textového pole.

    ![Schémata se tlačítko "Přidat" a "velký" zvýrazněnou](media/logic-apps-enterprise-integration-schemas/schema-largefile.png)

4. V **schémata** okna účtu pro integraci, by se měla zobrazit nově přidané schéma.

    ![Účtu integrace s "Schémata" a zvýrazní nové schéma](media/logic-apps-enterprise-integration-schemas/schema-41.png)

## <a name="edit-schemas"></a>Upravit schémata

1. Zvolte **schémata** dlaždici.

2. Po **schémata** otevře se okno, vyberte schéma, které chcete upravit.

3. Na **schémata** okně zvolte **upravit**.

    ![Okno schémata](media/logic-apps-enterprise-integration-schemas/edit-12.png)

4. Vyberte soubor schématu, kterou chcete upravit a pak vyberte **otevřít**.

    ![Otevřít schéma soubor pro úpravy](media/logic-apps-enterprise-integration-schemas/edit-31.png)

Azure zobrazuje zpráva, že schéma se úspěšně nahrál.

## <a name="delete-schemas"></a>Odstranit schémata

1. Zvolte **schémata** dlaždici.

2. Po **schémata** otevře se okno, vyberte schéma, které chcete odstranit.

3. Na **schémata** okně zvolte **odstranit**.

    ![Okno schémata](media/logic-apps-enterprise-integration-schemas/delete-12.png)

4. Pokud chcete potvrdit, že chcete odstranit vybrané schéma, zvolte **Ano**.

    !["Odstranit schéma" potvrzení](media/logic-apps-enterprise-integration-schemas/delete-21.png)

    V **schémata** okně seznamu schéma se aktualizuje a již obsahuje schéma, které jste odstranili.

    ![Vaše integrační účet s "Schémata" zvýrazněnou](media/logic-apps-enterprise-integration-schemas/delete-31.png)

## <a name="next-steps"></a>Další postup
* [Další informace o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "přečtěte si víc o enterprise integration pack").  


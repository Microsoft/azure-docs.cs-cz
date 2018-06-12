---
title: Schémata pro ověření XML - Azure Logic Apps | Microsoft Docs
description: Ověření XML – dokumenty s schémata pro Azure Logic Apps a Enterprise integračního balíčku
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: jeconnoc
editor: cgronlun
ms.assetid: 56c5846c-5d8c-4ad4-9652-60b07aa8fc3b
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: e23500ec3c16e66b8dc74fcba29e9b58f0b41790
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299058"
---
# <a name="validate-xml-with-schemas-for-azure-logic-apps-and-the-enterprise-integration-pack"></a>Ověření XML s schémata pro Azure Logic Apps a Enterprise integračního balíčku

Schémata potvrdit, že dokumenty XML, které se zobrazí jsou platné a mít očekávaná data v předdefinovaném formátu. Schémata také pomůže ověřit zprávy, které se vyměňují ve scénáři B2B.

## <a name="add-a-schema"></a>Přidat schéma.

1. Na portálu Azure vyberte **všechny služby**.

    ![Portál Azure, "Všechny služby"](media/logic-apps-enterprise-integration-schemas/overview-11.png)

2. Do vyhledávacího pole filtru zadejte **integrace**a vyberte **účty pro integraci** ze seznamu výsledků.

    ![Pole filtru hledání](media/logic-apps-enterprise-integration-schemas/overview-21.png)

3. Vyberte **integrace účet** ve které chcete přidat schéma.

    ![Seznam účtů, integrace](media/logic-apps-enterprise-integration-schemas/overview-31.png)

4. Vyberte **schémata** dlaždici.

    ![Příklad integrace účet "Schémata"](media/logic-apps-enterprise-integration-schemas/schema-11.png)

### <a name="add-a-schema-file-smaller-than-2-mb"></a>Přidejte soubor schématu, která je menší než 2 MB

1. V **schémata** okno, které se otevře (z předchozího postupu), zvolte **přidat**.

    ![Schémata okně "Přidat"](media/logic-apps-enterprise-integration-schemas/schema-21.png)

2. Zadejte název pro schéma. Nahrání souboru schématu výběrem ikony složku do **schématu** pole. Po dokončení procesu nahrávání, vyberte **OK**.

    ![Snímek obrazovky "Přidat schéma", se zvýrazněnou "malý soubor"](media/logic-apps-enterprise-integration-schemas/schema-31.png)

### <a name="add-a-schema-file-larger-than-2-mb-up-to-8-mb-maximum"></a>Přidejte soubor schématu, která je větší než 2 MB (maximálně 8 MB)

Na úrovni přístupu kontejneru objektu blob se liší podle těchto kroků: **veřejné** nebo **žádné anonymní přístup**.

**Chcete-li zjistit tato úroveň přístupu**

1.  Otevřete **Azure Storage Explorer**. 

2.  V části **kontejnery objektů Blob**, vyberte kontejner objektů blob, který chcete. 

3.  Vyberte **zabezpečení**, **úroveň přístupu**.

Pokud je úroveň zabezpečení přístupu k objektu blob **veřejné**, postupujte podle těchto kroků.

![Azure Storage Explorer "Kontejnery objektů Blob", "Zabezpečení" a "Veřejná" zvýrazněná](media/logic-apps-enterprise-integration-schemas/blob-public.png)

1. Odeslat schématu do svého účtu úložiště a zkopírujte identifikátor URI.

    ![Účet úložiště se zvýrazněnou identifikátor URI](media/logic-apps-enterprise-integration-schemas/schema-blob.png)

2. V **přidat schéma**, vyberte **velkých souborů**a zadejte identifikátor URI v **obsahu URI** textové pole.

    ![Schémata s tlačítko "Přidat" a "Velkých souborů" zvýrazněná](media/logic-apps-enterprise-integration-schemas/schema-largefile.png)

Pokud je úroveň zabezpečení přístupu k objektu blob **žádné anonymní přístup**, postupujte podle těchto kroků.

![Azure Storage Explorer "Kontejnery objektů Blob", "Zabezpečení" a "Žádná anonymní přístup" zvýrazněná](media/logic-apps-enterprise-integration-schemas/blob-1.png)

1. Schéma odešlete do svého účtu úložiště.

    ![Účet úložiště](media/logic-apps-enterprise-integration-schemas/blob-3.png)

2. Vygenerujte sdílený přístupový podpis pro schéma.

    ![Účet úložiště se zvýrazněnou kartu podpisy sdíleného přístupu](media/logic-apps-enterprise-integration-schemas/blob-2.png)

3. V **přidat schéma**, vyberte **velkých souborů**a zadejte sdílený přístupový podpis URI v **obsahu URI** textové pole.

    ![Schémata s tlačítko "Přidat" a "Velkých souborů" zvýrazněná](media/logic-apps-enterprise-integration-schemas/schema-largefile.png)

4. V **schémata** okno účtu integrace, by se měla zobrazit vaše nově přidané schéma.

    ![Účtu integrace s "Schémat" a nové schéma zvýrazněná](media/logic-apps-enterprise-integration-schemas/schema-41.png)

## <a name="edit-schemas"></a>Upravit schémat.

1. Vyberte **schémata** dlaždici.

2. Po **schémata** otevře se okno, vyberte schéma, které chcete upravit.

3. Na **schémata** okně zvolte **upravit**.

    ![Okno schémat.](media/logic-apps-enterprise-integration-schemas/edit-12.png)

4. Vyberte soubor schématu, který chcete upravit a pak vyberte **otevřete**.

    ![Soubor otevřete schématu upravit](media/logic-apps-enterprise-integration-schemas/edit-31.png)

Azure zobrazí zpráva, že schéma se úspěšně nahrál.

## <a name="delete-schemas"></a>Odstranit schémat.

1. Vyberte **schémata** dlaždici.

2. Po **schémata** otevře se okno, vyberte schéma, které chcete odstranit.

3. Na **schémata** okně zvolte **odstranit**.

    ![Okno schémat.](media/logic-apps-enterprise-integration-schemas/delete-12.png)

4. Pokud chcete potvrdit, že chcete odstranit vybrané schéma, zvolte **Ano**.

    ![Potvrzovací zpráva "Odstranit schématu"](media/logic-apps-enterprise-integration-schemas/delete-21.png)

    V **schémata** okně seznamu schématu aktualizuje a už obsahuje schéma, které jste odstranili.

    ![Svoji integraci účet s "Schémata" zvýrazněná](media/logic-apps-enterprise-integration-schemas/delete-31.png)

## <a name="next-steps"></a>Další postup
* [Další informace o integračního balíčku Enterprise](logic-apps-enterprise-integration-overview.md "Další informace o integračního balíčku enterprise").  


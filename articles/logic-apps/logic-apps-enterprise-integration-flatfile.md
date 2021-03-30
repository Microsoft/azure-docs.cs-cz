---
title: Kódování a dekódování plochých souborů
description: Kódování a dekódování plochých souborů pro podnikovou integraci v Azure Logic Apps pomocí Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 05/09/2020
ms.openlocfilehash: aebce8f284ed4bb21d99efffc8dd6d0c51b39533
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87001481"
---
# <a name="encode-and-decode-flat-files-in-azure-logic-apps-by-using-the-enterprise-integration-pack"></a>Kódování a dekódování plochých souborů v Azure Logic Apps s využitím řešení Enterprise Integration Pack

Před odesláním obsahu XML obchodnímu partnerovi ve scénáři B2B (Business-to-Business) možná budete chtít takový obsah nejdřív zakódovat. Vytvořením aplikace logiky můžete kódovat a dekódovat ploché soubory pomocí konektoru **plochých souborů** . Aplikace logiky může získat tento obsah XML z různých zdrojů, jako je Trigger žádosti, jiná aplikace nebo jiné [konektory, které podporuje Azure Logic Apps](../connectors/apis-list.md). Další informace najdete v tématu [co je Azure Logic Apps](logic-apps-overview.md)?

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud předplatné nemáte, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Aplikace logiky, ve které chcete použít konektor **plochých souborů** a Trigger, který spouští pracovní postup vaší aplikace logiky. Konektor **plochých souborů** poskytuje pouze akce, nikoli triggery. Můžete použít Trigger nebo jinou akci k zakládání obsahu XML do aplikace logiky pro kódování nebo dekódování. Pokud s Logic Apps začínáte, Projděte si [rychlý Start: Vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* [Účet pro integraci](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) , který je přidružený k vašemu předplatnému Azure a [propojený s aplikací logiky](./logic-apps-enterprise-integration-create-integration-account.md#link-account) , ve které plánujete použít konektor **plochých souborů** . Vaše aplikace logiky i účet pro integraci musí existovat ve stejném umístění nebo oblasti Azure.

* [Schéma](logic-apps-enterprise-integration-schemas.md) plochého souboru, které jste nahráli do účtu pro integraci pro kódování nebo dekódování obsahu XML

* Aspoň dva [obchodní partneři](logic-apps-enterprise-integration-partners.md) , které jste už ve svém účtu pro integraci definovali.

## <a name="add-flat-file-encode-action"></a>Přidání akce kódování plochého souboru

1. V [Azure Portal](https://portal.azure.com)otevřete aplikaci logiky v návrháři aplikace logiky.

1. V rámci triggeru nebo akce v aplikaci logiky vyberte **Nový krok**  >  **přidat akci**. V tomto příkladu se používá aktivační událost žádosti, která je pojmenována **při přijetí požadavku HTTP**, a zpracovává příchozí požadavky mimo aplikaci logiky.

   > [!TIP]
   > Zadání schématu JSON je volitelné. Pokud máte ukázkovou datovou část z příchozího požadavku, vyberte **použít ukázkovou datovou část k vygenerování schématu**, zadejte ukázkovou datovou část a vyberte **Hotovo**. Schéma se zobrazí v poli **schématu JSON textu žádosti** .

1. V části **zvolit akci** zadejte `flat file` . V seznamu akce vyberte tuto akci: **kódování plochého souboru**

   ![Vybrat akci kódování plochého souboru](./media/logic-apps-enterprise-integration-flatfile/select-flat-file-encoding.png)

1. Klikněte do pole **obsah** , aby se zobrazil seznam dynamického obsahu. V seznamu v části **při přijetí požadavku HTTP** vyberte vlastnost **text** , která obsahuje výstup textu žádosti z triggeru a obsah ke kódování.

   ![Vybrat obsah ke kódování ze seznamu dynamického obsahu](./media/logic-apps-enterprise-integration-flatfile/select-content-to-encode.png)

   > [!TIP]
   > Pokud se vlastnost **text** v seznamu dynamického obsahu nezobrazuje, vyberte **Zobrazit více** vedle popisku oddílu **když se přijme požadavek HTTP** .
   > Do pole **obsah** můžete také přímo zadat obsah k dekódování.

1. V seznamu **název schématu** vyberte schéma, které je v propojeném účtu integrace, které se má použít pro kódování, například:

   ![Vyberte schéma, které se má použít pro kódování.](./media/logic-apps-enterprise-integration-flatfile/select-schema-for-encoding.png)

   > [!NOTE]
   > Pokud se v seznamu nezobrazí žádné schéma, váš účet pro integraci neobsahuje žádné soubory schématu, které by bylo možné použít pro kódování. Nahrajte schéma, které chcete použít pro účet pro integraci.

1. Uložte aplikaci logiky. Chcete-li otestovat konektor, vyžádejte si požadavek na koncový bod HTTPS, který se zobrazí ve vlastnosti **Adresa URL příspěvku http** triggeru požadavku, a zahrňte do textu žádosti obsah XML, který chcete kódovat.

Nyní jste hotovi s nastavením akce kódování plochého souboru. V reálné aplikaci můžete ukládat zakódovaná data do obchodní aplikace (LOB), jako je třeba Salesforce. Nebo můžete zakódovaná data odeslat obchodnímu partnerovi. Pokud chcete poslat výstup z akce kódování do Salesforce nebo do svého obchodního partnera, použijte další [konektory, které jsou dostupné v Azure Logic Apps](../connectors/apis-list.md).

## <a name="add-flat-file-decode-action"></a>Přidání akce dekódování plochého souboru

1. V [Azure Portal](https://portal.azure.com)otevřete aplikaci logiky v návrháři aplikace logiky.

1. V rámci triggeru nebo akce v aplikaci logiky vyberte **Nový krok**  >  **přidat akci**. V tomto příkladu se používá aktivační událost žádosti, která je pojmenována **při přijetí požadavku HTTP**, a zpracovává příchozí požadavky mimo aplikaci logiky.

   > [!TIP]
   > Zadání schématu JSON je volitelné. Pokud máte ukázkovou datovou část z příchozího požadavku, vyberte **použít ukázkovou datovou část k vygenerování schématu**, zadejte ukázkovou datovou část a vyberte **Hotovo**. Schéma se zobrazí v poli **schématu JSON textu žádosti** .

1. V části **zvolit akci** zadejte `flat file` . V seznamu akce vyberte tuto akci: **dekódování plochého souboru**

   ![Výběr akce "dekódování plochého souboru"](./media/logic-apps-enterprise-integration-flatfile/select-flat-file-decoding.png)

1. Klikněte do pole **obsah** , aby se zobrazil seznam dynamického obsahu. V seznamu v části **při přijetí požadavku HTTP** vyberte vlastnost **text** , která obsahuje výstup textu žádosti z triggeru a obsah k dekódování.

   ![Vyberte obsah, který se má dekódovat ze seznamu dynamického obsahu.](./media/logic-apps-enterprise-integration-flatfile/select-content-to-decode.png)

   > [!TIP]
   > Pokud se vlastnost **text** v seznamu dynamického obsahu nezobrazuje, vyberte **Zobrazit více** vedle popisku oddílu **když se přijme požadavek HTTP** . Do pole **obsah** můžete také přímo zadat obsah k dekódování.

1. V seznamu **název schématu** vyberte schéma, které je v propojeném účtu integrace, které se má použít pro dekódování, například:

   ![Vyberte schéma, které se má použít pro dekódování.](./media/logic-apps-enterprise-integration-flatfile/select-schema-for-decoding.png)

   > [!NOTE]
   > Pokud se v seznamu nezobrazí žádné schéma, váš účet pro integraci neobsahuje žádné soubory schématu, které by bylo možné použít k dekódování. Nahrajte schéma, které chcete použít pro účet pro integraci.

1. Uložte aplikaci logiky. Chcete-li otestovat konektor, vyžádejte si požadavek na koncový bod HTTPS, který se zobrazí ve vlastnosti **Adresa URL příspěvku http** triggeru požadavku, a zahrňte do textu žádosti obsah XML, který chcete dekódovat.

Nyní jste hotovi s nastavením akce dekódování plochého souboru. V reálné aplikaci můžete ukládat dekódovat data do obchodní aplikace (LOB), jako je třeba Salesforce. Případně můžete dekódovat data odeslat obchodnímu partnerovi. Pokud chcete odeslat výstup z akce dekódování do Salesforce nebo svého obchodního partnera, použijte další [konektory, které jsou k dispozici v Azure Logic Apps](../connectors/apis-list.md).

## <a name="next-steps"></a>Další kroky

* Další informace o [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)

---
title: Dekódování zprávy AS2 – Azure Logic Apps | Dokumentace Microsoftu
description: Jak používat dekodéru AS2 v Enterprise Integration Pack pro Azure Logic Apps
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: jeconnoc
editor: ''
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2018
ms.author: LADocs; padmavc
ms.openlocfilehash: a1b2d68a4a9433dc5c68d65552bf6bd509463958
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2018
ms.locfileid: "42060733"
---
# <a name="decode-as2-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Dekódování zprávy AS2 pro Azure Logic Apps sadou Enterprise Integration Pack 

K vytvoření, zabezpečení a spolehlivost při přenosu zprávy, použití konektoru zpráva dekódování AS2. Tento konektor poskytuje digitální podpis, dešifrování a potvrzování prostřednictvím zprávy dispozice upozornění (zprávy MDN.).

## <a name="before-you-start"></a>Než začnete

Tady je položky, které budete potřebovat:

* Účet Azure. můžete vytvořit [bezplatný účet](https://azure.microsoft.com/free)
* [Účtu pro integraci](logic-apps-enterprise-integration-create-integration-account.md) , který již má definovaný a spojené s předplatným Azure. Musíte mít účtu pro integraci k používání konektoru AS2 dekódování zprávy.
* Alespoň dva [partnery](logic-apps-enterprise-integration-partners.md) , která jsou již definovány v účtu integrace
* [Smlouvy AS2](logic-apps-enterprise-integration-as2.md) , která je již definována v účtu integrace

## <a name="decode-as2-messages"></a>Dekódování zprávy AS2

1. [Vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

2. Dekódování AS2 zpráva konektoru nemá aktivačních událostí, proto musíte přidat aktivační událost pro spuštění aplikace logiky jako aktivační událost požadavek. V návrháři aplikace logiky přidávat aktivační události a pak přidat akci do aplikace logiky.

3.  Do vyhledávacího pole zadejte "AS2" filtr. Vyberte **AS2 - zpráva dekódování AS2**.
   
    ![Vyhledejte "AS2"](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage1.png)

4. Pokud jste nevytvořili dříve všechna připojení k účtu pro integraci, budete vyzváni k vytvoření tohoto připojení. Název připojení a vyberte účet pro integraci, kterou chcete připojit.
   
    ![Vytvoření připojení integrace](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage2.png)

    Vyžadují se vlastnosti s hvězdičkou.

    | Vlastnost | Podrobnosti |
    | --- | --- |
    | Název připojení * |Zadejte libovolný název vašeho připojení. |
    | Účet integrace * |Zadejte název účtu pro integraci. Ujistěte se, že integrační účet a logiku aplikace jsou ve stejném umístění Azure. |

5.  Až budete hotovi, vaše podrobnosti připojení by měl vypadat podobně jako tento příklad. Chcete-li dokončit vytváření připojení, zvolte **vytvořit**.

    ![Podrobnosti připojení integrace](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage3.png)

6. Po vytvoření připojení, jak je znázorněno v tomto příkladu, vyberte **tělo** a **záhlaví** z výstupů požadavku.
   
    ![vytvořené připojení integrace](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage4.png) 

    Příklad:

    ![Vyberte záhlaví a text z výstupů žádosti](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage5.png) 


## <a name="as2-decoder-details"></a>Podrobnosti dekodéru AS2

Dekódování AS2 connector provádí tyto úlohy: 

* Zpracovává hlavičky AS2/HTTP
* Ověří podpis (je-li konfigurováno)
* Dešifruje zprávy (je-li konfigurováno)
* Dekomprimuje zprávy (je-li konfigurováno)
* Zkontrolujte a zakázat duplicity ID zpráv (je-li konfigurováno)
* Sloučí přijaté zprávy MDN s původní odchozí zprávy
* Aktualizace a koreluje záznamy v databázi – popírání odpovědnosti
* Zapíše záznamy pro generování sestav o stavu AS2
* Obsah výstupní datovou část je kódování base64
* Určuje, jestli je to požadováno, zprávy MDN a zda MDN by měl být synchronní nebo asynchronní podle konfigurace v smlouvy AS2
* Generuje synchronní nebo asynchronní zprávy MDN (podle dohody konfigurace)
* Nastaví vlastnosti a tokenů korelace v MDN


  > [!NOTE]
  > Pokud používáte Azure Key Vault pro správu certifikátů, ujistěte se, že nakonfigurujete klíčů tak, aby povolovala **dešifrovat** operace.
  > V opačném případě se nezdaří dekódování AS2.
  >
  > ![Dešifruje trezor klíčů](media/logic-apps-enterprise-integration-as2-decode/keyvault1.png)

## <a name="try-this-sample"></a>Zkuste tuto ukázku

Vyzkoušejte si nasazení scénář plně funkční logiku aplikace a ukázky AS2, najdete v článku [AS2 šablony aplikace logiky a scénář](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/).

## <a name="view-the-swagger"></a>Zobrazení swaggeru
Zobrazit [swagger podrobnosti](/connectors/as2/). 

## <a name="next-steps"></a>Další postup
[Další informace o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md) 


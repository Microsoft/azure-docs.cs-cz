---
title: Kódování zprávy AS2 – Azure Logic Apps | Dokumentace Microsoftu
description: Jak používat kodér AS2 v Enterprise Integration Pack pro Azure Logic Apps
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: jeconnoc
editor: ''
ms.assetid: 332fb9e3-576c-4683-bd10-d177a0ebe9a3
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2018
ms.author: LADocs; padmavc
ms.openlocfilehash: b3b57b2505c4185f3a81530cbc9eeb464dcfa518
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2018
ms.locfileid: "42054817"
---
# <a name="encode-as2-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Kódování zprávy AS2 pro Azure Logic Apps sadou Enterprise Integration Pack

K vytvoření, zabezpečení a spolehlivost při přenosu zprávy, použití konektoru zpráv kódování AS2. Tento konektor poskytuje digitální podpis, šifrování a potvrzování prostřednictvím zprávy dispozice upozornění (zprávy MDN.), což také vede k podpoře pro Nepopiratelnost odpovědnosti.

## <a name="before-you-start"></a>Než začnete

Tady je položky, které budete potřebovat:

* Účet Azure. můžete vytvořit [bezplatný účet](https://azure.microsoft.com/free)
* [Účtu pro integraci](logic-apps-enterprise-integration-create-integration-account.md) , který již má definovaný a spojené s předplatným Azure. Musíte mít účtu pro integraci k používání konektoru AS2 kódování zprávy.
* Alespoň dva [partnery](logic-apps-enterprise-integration-partners.md) , která jsou již definovány v účtu integrace
* [Smlouvy AS2](logic-apps-enterprise-integration-as2.md) , která je již definována v účtu integrace

## <a name="encode-as2-messages"></a>Kódování zpráv AS2

1. [Vytvoření aplikace logiky](quickstart-create-first-logic-app-workflow.md).

2. Zprávy konektoru AS2 kódování nemá aktivačních událostí, takže je nutné přidat aktivační událost pro spuštění aplikace logiky jako aktivační událost požadavek. V návrháři aplikace logiky přidávat aktivační události a pak přidat akci do aplikace logiky.

3.  Do vyhledávacího pole zadejte "AS2" filtr. Vyberte **AS2 - zpráv kódování AS2**.
   
    ![Vyhledejte "AS2"](./media/logic-apps-enterprise-integration-as2-encode/as2decodeimage1.png)

4. Pokud jste nevytvořili dříve všechna připojení k účtu pro integraci, budete vyzváni k vytvoření tohoto připojení. Název připojení a vyberte účet pro integraci, kterou chcete připojit. 
   
    ![Vytvoření připojení k účtu pro integraci](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage1.png)  

    Vyžadují se vlastnosti s hvězdičkou.

    | Vlastnost | Podrobnosti |
    | --- | --- |
    | Název připojení * |Zadejte libovolný název vašeho připojení. |
    | Účet integrace * |Zadejte název účtu pro integraci. Ujistěte se, že integrační účet a logiku aplikace jsou ve stejném umístění Azure. |

5.  Až budete hotovi, vaše podrobnosti připojení by měl vypadat podobně jako tento příklad. Chcete-li dokončit vytváření připojení, zvolte **vytvořit**.
   
    ![Podrobnosti připojení integrace](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage2.png)

6. Po vytvoření připojení, jak je znázorněno v tomto příkladu, zadejte podrobnosti pro **AS2-od**, **AS2-na identifikátory** nakonfigurované ve vaší smlouvě a **text**, což je datovou část zprávy.
   
    ![Zadejte povinné pole](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage3.png)

## <a name="as2-encoder-details"></a>Podrobnosti kodéru AS2

Kódování AS2 connector provádí tyto úlohy: 

* Platí hlavičky AS2/HTTP
* Příznaky odchozí zprávy (je-li konfigurováno)
* Šifruje odchozí zprávy (je-li konfigurováno)
* Komprimuje zprávy (je-li konfigurováno)
* Název souboru v hlavičce MIME přenášet (je-li konfigurováno)


  > [!NOTE]
  > Pokud používáte Azure Key Vault pro správu certifikátů, ujistěte se, že nakonfigurujete klíčů tak, aby povolovala **šifrovat** operace.
  > V opačném případě se nezdaří kódování AS2.
  >
  > ![Dešifruje trezor klíčů](media/logic-apps-enterprise-integration-as2-encode/keyvault1.png)

## <a name="try-this-sample"></a>Zkuste tuto ukázku

Vyzkoušejte si nasazení scénář plně funkční logiku aplikace a ukázky AS2, najdete v článku [AS2 šablony aplikace logiky a scénář](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/).

## <a name="view-the-swagger"></a>Zobrazení swaggeru
Zobrazit [swagger podrobnosti](/connectors/as2/). 

## <a name="next-steps"></a>Další postup
[Další informace o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "přečtěte si víc o Enterprise Integration Pack") 


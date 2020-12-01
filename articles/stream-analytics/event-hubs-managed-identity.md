---
title: Použití spravovaných identit k přístupu k centru událostí z Azure Stream Analytics úlohy (Preview)
description: Tento článek popisuje, jak pomocí spravovaných identit ověřit vaši Azure Stream Analyticsovou úlohu do Azure Event Hubs vstupu a výstupu.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 11/30/2020
ms.openlocfilehash: c65833e5bf581c6326bf9362c7e5fc00a985d301
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2020
ms.locfileid: "96355028"
---
# <a name="use-managed-identities-to-access-event-hubfrom-an-azure-stream-analytics-job-preview"></a>Použití spravovaných identit k přístupu k centru událostí z Azure Stream Analytics úlohy (Preview)

Azure Stream Analytics podporuje spravované ověřování identity pro Azure Event Hubs Input i Output. Spravované identity odstraňují omezení metod ověřování založených na uživatelích, například nutnost opětovného ověření z důvodu změny hesla nebo vypršení platnosti tokenu uživatele, ke kterému dochází každých 90 dní. Když odeberete nutnost ručního ověřování, vaše nasazení Stream Analytics můžou být plně automatizovaná.  

Spravovaná identita je spravovaná aplikace zaregistrovaná v Azure Active Directory, která představuje danou Stream Analytics úlohu. Spravovaná aplikace se používá k ověření cílového prostředku, včetně Event Hubs za bránou firewall nebo virtuální sítí (VNet). Další informace o tom, jak obejít brány firewall, najdete v tématu [Povolení přístupu k oborům názvů Azure Event Hubs prostřednictvím privátních koncových bodů](../event-hubs/private-link-service.md#trusted-microsoft-services).

V tomto článku se dozvíte, jak povolit spravovanou identitu pro Event Hubs vstup nebo výstup Stream Analyticsch úloh prostřednictvím Azure Portal.Než povolíte spravovanou identitu, musíte nejdřív mít prostředek Stream Analytics úlohy a centra událostí.

## <a name="create-a-managedidentity"></a>Vytvoření spravované identity  

Nejdřív vytvoříte spravovanou identitu pro svou Azure Stream Analytics úlohu.  

1. V Azure Portal otevřete Azure Stream Analytics úlohu.  

1. V levém navigačním panelu vyberte **spravovanou identitu**   nacházející se v části *Konfigurovat*. Potom zaškrtněte políčko vedle **použít spravovanou identitu přiřazenou systémem**   a vyberte **Uložit**.

   :::image type="content" source="media/event-hubs-managed-identity/system-assigned-managed-identity.png" alt-text="Spravovaná identita přiřazená systémem":::  

1. Instanční objekt pro identitu Stream Analytics úlohy se vytvoří v Azure Active Directory. Životní cyklus nově vytvořené identity se spravuje v Azure. Po odstranění úlohy Stream Analytics se služba Azure automaticky odstraní přidružená identita (tj. instanční objekt).  

   Při uložení konfigurace se ID objektu (OID) instančního objektu zobrazí jako ID objektu zabezpečení, jak je znázorněno níže:  

   :::image type="content" source="media/event-hubs-managed-identity/principal-id.png" alt-text="ID objektu zabezpečení":::

   Instanční objekt má stejný název jako Stream Analytics úloha. Například pokud je název úlohy  `MyASAJob` , název instančního objektu také  `MyASAJob` .  

## <a name="grant-the-stream-analytics-job-permissionsto-access-the-event-hub"></a>Udělení oprávnění úlohy Stream Analytics k přístupu do centra událostí

Aby úloha Stream Analytics měla přístup k centru událostí pomocí spravované identity, musí mít vytvořený instanční objekt zvláštní oprávnění k centru událostí.

1. V centru událostí přejít na **Access Control (IAM)** .

1. Vyberte **+ Přidat** a **přidejte přiřazení role**.

1. Na stránce **Přidat přiřazení role** zadejte následující možnosti:

   |Parametr|Hodnota|
   |---------|-----|
   |Role|Vlastník dat Event Hubs Azure|
   |Přiřadit přístup|Uživatel, skupina nebo instanční objekt|
   |Vyberte|Zadejte název vaší Stream Analytics úlohy.|

   :::image type="content" source="media/event-hubs-managed-identity/add-role-assignment.png" alt-text="Přidat přiřazení role":::

1. Vyberte **Uložit** a počkejte minutu, aby se změny rozšířily.

Tuto roli můžete také udělit na úrovni oboru názvů centra událostí, která bude přirozeně šířit oprávnění pro všechny Event Hubs vytvořené pod ní. To znamená, že všechny Event Hubs pod oborem názvů se dají v Stream Analytics úloze použít jako prostředek ověřování identity se spravovanými identitami.

## <a name="create-anevent-hub-input-or-output"></a>Vytvoření vstupu nebo výstupu centra událostí  

Teď, když je vaše spravovaná identita nakonfigurovaná, jste připraveni přidat prostředek centra událostí jako vstup nebo výstup do úlohy Stream Analytics.  

### <a name="add-the-event-hub-as-an-input"></a>Přidání centra událostí jako vstupu 

1. Přejděte do úlohy Stream Analytics a přejděte na stránku **vstupy** v části **topologie úloh**.

1. Vyberte **Přidat vstup streamu > centrum událostí**. V okně Vlastnosti vstupu vyhledejte a vyberte centrum událostí a v rozevírací nabídce *režim ověřování* vyberte **spravovaná identita** .

1. Vyplňte zbytek vlastností a vyberte **Uložit**.

### <a name="add-the-event-hub-as-an-output"></a>Přidat centrum událostí jako výstup

1. Přejděte do úlohy Stream Analytics a přejděte na stránku **výstupy** v části **topologie úloh**.

1. Vyberte **přidat > centrum událostí**. V okně Vlastnosti výstupu vyhledejte a vyberte centrum událostí a v rozevírací nabídce *režim ověřování* vyberte **spravovaná identita** .

1. Vyplňte zbytek vlastností a vyberte **Uložit**.

## <a name="next-steps"></a>Další kroky

* [Event Hubs výstup z Azure Stream Analytics](event-hubs-output.md)
* [Streamování dat z Event Hubs](stream-analytics-define-inputs.md#stream-data-from-event-hubs)
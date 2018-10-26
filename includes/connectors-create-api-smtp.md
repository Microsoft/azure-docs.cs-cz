---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: d90ef1737f0107e5e323c1e90e3f9021efd4e4f7
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50093105"
---
### <a name="prerequisites"></a>Požadavky
* A [SMTP](https://wikipedia.org/wiki/Simple_Mail_Transfer_Protocol) účtu  

Předtím, než v aplikaci logiky můžete používat svůj účet SMTP, musíte se autorizovat aplikaci logiky se připojit ke svému účtu SMTP. Naštěstí vám pomůžou snadno z v rámci aplikace logiky na webu Azure Portal.  

Tady jsou kroky k autorizaci aplikace logiky se připojit ke svému účtu SMTP:  

1. Chcete-li vytvořit připojení k SMTP serveru, v návrháři aplikace logiky, vyberte **rozhraní API spravovaná Microsoftem zobrazit** v rozevíracím seznamu zadejte *SMTP* do vyhledávacího pole. Výběr triggeru nebo akce, které budete chtít použít:  
   ![](./media/connectors-create-api-smtp/smtp-1.png)  
2. Pokud jste ještě nevytvořili žádná připojení k SMTP serveru před, zobrazí výzva k zadání přihlašovacích údajů SMTP. Tyto přihlašovací údaje se použijí k autorizaci aplikace logiky pro připojení k a přístup k datům účet SMTP:  
   ![](./media/connectors-create-api-smtp/smtp-2.png)  
3. Všimněte si, připojení bylo vytvořeno a teď jste zdarma postupujte podle dalších kroků ve vaší aplikaci logiky:  
   ![](./media/connectors-create-api-smtp/smtp-3.png)  


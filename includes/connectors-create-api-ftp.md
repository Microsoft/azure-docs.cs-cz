---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: 65f1e6d2489775a17ba2dacef0623706364fffab
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50091769"
---
### <a name="prerequisites"></a>Požadavky
* [FTP](https://wikipedia.org/wiki/File_Transfer_Protocol) účtu  

Než účtu FTP můžete použít v aplikaci logiky, musíte se autorizovat aplikaci logiky pro připojení k účtu FTP. Naštěstí vám pomůžou snadno z v rámci aplikace logiky na webu Azure Portal.  

Tady jsou kroky k autorizaci aplikace logiky pro připojení k účtu FTP:  

1. Chcete-li vytvořit připojení k serveru FTP, v návrháři aplikace logiky, vyberte **rozhraní API spravovaná Microsoftem zobrazit** v rozevíracím seznamu zadejte *FTP* do vyhledávacího pole. Výběr triggeru nebo akce, které budete chtít použít:  
   ![Krok vytvoření připojení FTP](./media/connectors-create-api-ftp/ftp-1.png)  
2. Pokud jste ještě nevytvořili žádná připojení k serveru FTP před, zobrazí výzva zadat přihlašovací údaje serveru FTP. Tyto přihlašovací údaje se použijí k autorizaci aplikace logiky pro připojení k a přístup k datům v účtu FTP:  
   ![Krok vytvoření připojení FTP](./media/connectors-create-api-ftp/ftp-2.png)  
3. Všimněte si, připojení bylo vytvořeno a teď jste zdarma postupujte podle dalších kroků ve vaší aplikaci logiky:  
   ![Krok vytvoření připojení FTP](./media/connectors-create-api-ftp/ftp-3.png)  


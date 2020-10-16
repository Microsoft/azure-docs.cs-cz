---
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/29/2020
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: 73a8f20122e21c4557289995c15b008401285011
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131645"
---
V položce seznamu ACL vždy používejte [skupiny zabezpečení Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups) jako přiřazený objekt zabezpečení. Naodporujte příležitosti k přímému přiřazení jednotlivých uživatelů nebo instančních objektů. Pomocí této struktury budete moct přidávat a odebírat uživatele nebo instanční objekty, aniž byste museli znovu použít seznamy ACL pro celou adresářovou strukturu. Místo toho můžete přidat nebo odebrat uživatele a instanční objekty z příslušné skupiny zabezpečení služby Azure AD. 

Existuje mnoho různých způsobů, jak nastavit skupiny. Představte si například, že máte adresář s názvem **/LogData** , který obsahuje data protokolu generovaná vaším serverem. Azure Data Factory (ADF) ingestuje data do této složky. Konkrétní uživatelé z týmu Service Engineering budou nahrávat protokoly a spravovat ostatní uživatele této složky a různé clustery datacihly budou analyzovat protokoly z této složky. 

Pokud chcete tyto aktivity povolit, můžete vytvořit `LogsWriter` skupinu a `LogsReader` skupinu. Pak můžete přiřadit oprávnění následujícím způsobem:

- Přidejte `LogsWriter` skupinu do seznamu ACL adresáře **/LogData** s `rwx` oprávněními.
- Přidejte `LogsReader` skupinu do seznamu ACL adresáře **/LogData** s `r-x` oprávněními.
- Přidejte instanční objekt služby nebo Identita spravované služby (MSI) pro ADF do `LogsWriters` skupiny.
- Přidejte uživatele v rámci technického týmu služeb do `LogsWriter` skupiny.
- Do skupiny přidejte instanční objekt služby nebo MSI pro datacihly `LogsReader` .

Pokud uživatel v týmu technické služby opustí společnost, mohli byste je ze `LogsWriter` skupiny odebrat. Pokud jste tohoto uživatele nepřidali do skupiny, ale místo toho jste přidali vyhrazenou položku seznamu ACL pro tohoto uživatele, bude nutné odebrat tuto položku seznamu řízení přístupu z adresáře **/LogData** . Museli byste taky odebrat položku ze všech podadresářů a souborů v celé hierarchii adresáře adresáře **/LogData** . 

Chcete-li vytvořit skupinu a přidat členy, přečtěte si téma [Vytvoření základní skupiny a přidání členů pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal).
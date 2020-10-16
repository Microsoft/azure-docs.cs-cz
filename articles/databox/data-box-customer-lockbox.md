---
title: Konfigurovat bezpečnostní modul pro Azure Data Box
description: Naučte se používat Customer Lockbox s Azure Data Box.
services: databox
author: alkohli
ms.service: databox
ms.topic: how-to
ms.date: 07/10/2020
ms.author: alkohli
ms.subservice: pod
ms.openlocfilehash: 178ad169c1b576458f38b440ca79f4bb9eb012f5
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92124907"
---
# <a name="use-customer-lockbox-for-azure-data-box-preview"></a>Použít Customer Lockbox pro Azure Data Box (Preview)

Azure Data Box slouží k přenosu zákaznických dat do a z Azure. Existují případy, kdy podpora Microsoftu nemusí potřebovat přístup k zákaznickým datům během Support request. Můžete použít Customer Lockbox jako rozhraní pro kontrolu a schválení nebo odmítnutí těchto požadavků na přístup k datům. 

Tento článek popisuje, jak se Customer Lockbox požadavky zahajují a sledují pro Data Box import a export objednávek. Tento článek se týká Azure Data Boxch zařízení i Azure Data Box Heavy. 

## <a name="devops-workflow-for-data-access"></a>DevOps pracovní postup pro přístup k datům

Provozní tým podpory a Data Box v Microsoftu obvykle nepřistupuje k zákaznickým datům. Snaží se vyřešit problémy pomocí standardních nástrojů a telemetrie. <!--The only scenarios where there is a need to access customer data is when there is an issue with the data that needs to be fixed. For example, if the data is copied to a wrong folder or is in an incorrect format and is likely to result in an upload or download failure, then Microsoft will try to access your data in the Azure datacenter.--> 

Pokud nelze vyřešit problémy a vyžadovat podpora Microsoftu k prozkoumání nebo opravení dat, požadují se zvýšený přístup prostřednictvím portálu just in time (JIT). Portál JIP ověřuje úroveň oprávnění, poskytuje vícefaktorové ověřování a také obsahuje schválení interních schvalovatelů Microsoftu. Schvalovatel může být například DevOps Manager. 

Po schválení žádosti o přístup se zvýšeným oprávněním na portálu JIT, pokud jste povolili bezpečnostní modul, bude Microsoft taky vyžadovat explicitní souhlas s přístupem k datům. Přístup je požadován a sledován prostřednictvím služby Customer Lockbox na portálu. 

Pokud jste nepovolili bezpečnostní modul, není váš souhlas potřebný k přístupu k datům.


## <a name="prerequisites-for-access-request"></a>Předpoklady pro žádost o přístup

Než začnete, ujistěte se, že:

1. Vytvořili jste pořadí Azure Data Box podle pokynů v tématu:
    1. [Kurz: pořadí Azure Data box](data-box-deploy-ordered.md) pro import objednávek.
    1. [Kurz: pořadí Azure Data box](data-box-deploy-export-ordered.md) pro export objednávek.

2. Nakonfigurovali jste Customer Lockbox pro Data Box. Toto je služba výslovného souhlasu. 

    1. Customer Lockbox je v současnosti ve verzi Preview služby Data Box. Pokud chcete pro vaši organizaci povolit Customer Lockbox Data Box, zaregistrujte se [Customer Lockbox pro Public Preview Azure](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Kwz02N6XVCoKNpxIpqE_hUNzlTUUNYVkozOVlFNVRSWDVHRkkwTFQyViQlQCN0PWcu).
    2. Customer Lockbox je k dispozici automaticky pro všechny zákazníky, kteří mají plán podpory Azure s minimální úrovní vývojářů. <!--How do you enable Lockbox? change this for Azure Data Box, perhaps you need a different support plan When you have an eligible support plan, no action is required by you to enable Customer Lockbox. Customer Lockbox requests are initiated by a Microsoft engineer if this action is needed to progress a support ticket that is filed from somebody in your organization.-->

3. Pro tento problém je již otevřen požadavek služby nebo lístek podpory. Informace o lístku podpory najdete v tématu [File a Service Request for data box](data-box-disk-contact-microsoft-support.md).


## <a name="track-approve-request-via-lockbox"></a>Sledování, schvalování žádostí prostřednictvím bezpečnostního modulu

Pokud chcete sledovat a schvalovat žádost o přístup k zákaznickým datům, postupujte takto:

1. Microsoft zjistí, že při nahrávání nebo stahování dat v datacentru Azure dochází k potížím. Například pořadí Data Box je během fáze **kopírování dat** zastaveno. 

    Pracovník podpory se připojuje k Data Box prostřednictvím relace podpory a snaží se řešit potíže pomocí standardních nástrojů a telemetrie. Pokud jsou disky Data Box uzamčeny a sdílené složky nejsou k dispozici, pak pracovník podpory vytvoří žádost o bezpečnostní modul. 
 
2. Při vytvoření žádosti se obvykle oznámení dostane ke Správci předplatného, ale můžete také nakonfigurovat skupinu pro oznámení. 

3. Žádost o bezpečnostní modul můžete zobrazit v Azure Portal ke schválení. 

    ![Požadavek v Azure Portal](./media/data-box-customer-lockbox/3-lockbox-request-azure-portal.png)

    Pokud chcete žádost o bezpečnostní modul schválit z portálu, můžete vybrat **schválit**.

    ![Schválit žádost](./media/data-box-customer-lockbox/4-lockbox-request-details-azure-portal.png)


    Po schválení žádosti jsou disky zařízení odemčené a sdílené složky jsou dostupné v relaci podpory.

4. Pracovník podpory vyřeší problém s nahráváním a pak vypne relaci podpory.

Po vyřešení problému bude úloha kopírování dat dokončena až do dokončení.


## <a name="next-steps"></a>Další kroky

- [Customer Lockbox pro Microsoft Azure](../security/fundamentals/customer-lockbox-overview.md)

<!--- [Approve, audit support access requests to VMs using Customer Lockbox for Azure](https://azure.microsoft.com/blog/approve-audit-support-access-requests-to-vms-using-customer-lockbox-for-azure/)-->
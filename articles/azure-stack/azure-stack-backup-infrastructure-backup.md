---
title: Zálohování a obnovení dat pro Azure Stack pomocí infrastruktury služby Backup | Dokumentace Microsoftu
description: Zálohovat a obnovit konfiguraci a data service pomocí služby zálohování infrastruktury.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 9B51A3FB-EEFC-4CD8-84A8-38C52CFAD2E4
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 9/10/2018
ms.author: mabrigg
ms.reviewer: hectorl
ms.openlocfilehash: 9f2668ff84ade4ba99b7aa7dcd67feafadc1c6c4
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2018
ms.locfileid: "44377832"
---
# <a name="backup-and-data-recovery-for-azure-stack-with-the-infrastructure-backup-service"></a>Zálohování a obnovení dat pro Azure Stack pomocí infrastruktury služby Backup

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Zálohovat a obnovit konfiguraci a data service pomocí služby zálohování infrastruktury. Každá instalace služby Azure Stack obsahuje instanci služby. Zálohy vytvořené ve službě pro opětovné nasazení Azure Stack cloud můžete použít k obnovení identit, zabezpečení a data Azure Resource Manageru.

Zálohování můžete povolit, pokud budete chtít umístit své cloudové do produkčního prostředí. Nepovolujte zálohování, pokud máte v plánu provést testování a ověřování pro dlouhou dobu.

Před povolením služby backup, ujistěte se, že máte [požadavky splněné](#verify-requirements-for-the-infrastructure-backup-service).

> [!Note]  
> Služba Backup infrastruktury nezahrnuje aplikací a dat uživatele. Naleznete v následujících článcích pokyny o zálohování a obnovení [App Services](https://aka.ms/azure-stack-app-service), [SQL](https://aka.ms/azure-stack-ms-sql), a [MySQL](https://aka.ms/azure-stack-mysql) poskytovatelů prostředků a data přidruženého uživatele...

## <a name="the-infrastructure-backup-service"></a>Zálohovací služby infrastruktury

Služby obsahují následující funkce.

| Funkce                                            | Popis                                                                                                                                                |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Služby infrastruktury zálohování                     | Zálohování koordinaci mezi podmnožinou služby infrastruktury ve službě Azure Stack. Pokud dojde k havárii, je možné obnovit data v rámci opětovné nasazení. |
| Komprese a šifrování dat exportovaných zálohování | Zálohovaná data komprimovaná a šifrovaná systém předtím, než se exportují do umístění úložiště externí program od správce.                |
| Monitorování úlohy zálohování                              | Systém oznámení při selhání a nápravné kroky úlohy zálohování.                                                                                                |
| Prostředí správy zálohování                       | Zálohování RP podporuje povolení zálohování.                                                                                                                         |
| Cloudové zotavení                                     | Pokud dojde ke ztrátě dat, zálohování umožňuje obnovit základní informace o službě Azure Stack jako součást nasazení.                                 |

## <a name="verify-requirements-for-the-infrastructure-backup-service"></a>Ověřte požadavky pro službu Backup infrastruktury

- **Umístění úložiště**  
  Budete potřebovat sdílené složky přístupné z Azure Stack, který může obsahovat sedm zálohy. Každá záloha je přibližně 10 GB. Vaše sdílené složky měli být schopni uložit 140 GB místa zálohy. Další informace o výběru umístění úložiště pro zálohy služby Azure Stack infrastruktury, najdete v části [záložní řadič požadavky](azure-stack-backup-reference.md#backup-controller-requirements).
- **Přihlašovací údaje**  
  Budete potřebovat účet uživatele domény a přihlašovací údaje, například můžete použít přihlašovací údaje Správce služby Azure Stack.
- **Šifrovací klíč**  
  Záložní soubory jsou šifrované pomocí tohoto klíče. Ujistěte se, že tento klíč uložit na bezpečném místě. Po nastavení tohoto klíče poprvé nebo v budoucnu obměně klíče za nemůže zobrazit tento klíč z tohoto rozhraní. Další pokyny ke generování předsdíleným klíčem, postupujte podle skripty na [povolit zálohování pro Azure Stack s prostředím PowerShell](azure-stack-backup-enable-backup-powershell.md).

## <a name="next-steps"></a>Další postup

- Zjistěte, jak [povolit zálohování pro Azure Stack z portálu pro správu](azure-stack-backup-enable-backup-console.md).
- Zjistěte, jak [povolit zálohování pro Azure Stack s prostředím PowerShell](azure-stack-backup-enable-backup-powershell.md).
- Zjistěte, jak [zálohování Azure stacku](azure-stack-backup-back-up-azure-stack.md )
- Zjistěte, jak [obnovit ze ztráty dat](azure-stack-backup-recover-data.md)

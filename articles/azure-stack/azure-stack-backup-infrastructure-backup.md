---
title: Zálohování a obnovení dat pro Azure Stack pomocí infrastruktury služby Backup | Dokumentace Microsoftu
description: Zálohovat a obnovit konfiguraci a data service pomocí služby zálohování infrastruktury.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: mabrigg
ms.reviewer: hectorl
ms.lastreviewed: 03/19/2019
ms.openlocfilehash: da1ae76925ffeba7a1df57b4121f8cfe20b2887f
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2019
ms.locfileid: "58882102"
---
# <a name="backup-and-data-recovery-for-azure-stack-with-the-infrastructure-backup-service"></a>Zálohování a obnovení dat pro Azure Stack pomocí infrastruktury služby Backup

*Týká se Azure Stack integrované systémy a Azure Stack Development Kit*

Zálohovat a obnovit konfiguraci a data service pomocí služby zálohování infrastruktury. Každá instalace služby Azure Stack obsahuje instanci služby. Zálohy vytvořené ve službě pro opětovné nasazení Azure Stack cloud můžete použít k obnovení identit, zabezpečení a data Azure Resource Manageru. 

Zálohování můžete povolit, pokud budete chtít umístit své cloudové do produkčního prostředí. Nepovolujte zálohování, pokud máte v plánu provést testování a ověřování pro dlouhou dobu.

Před povolením služby backup, ujistěte se, že máte [požadavky splněné](#verify-requirements-for-the-infrastructure-backup-service).

> [!Note]  
> Služba Backup infrastruktury nezahrnuje aplikací a dat uživatele. Odkazovat na [chránit virtuální počítače nasazené ve službě Azure Stack](user/azure-stack-manage-vm-protect.md) Další informace o ochraně virtuálních počítačů IaaS na základě aplikací. Komplexnější přehled o tom, jak chránit aplikace ve službě Azure Stack, najdete [důležité informace o službě Azure Stack pro obchodní kontinuity podnikových procesů a po havárii obnovení dokument White Paper](https://aka.ms/azurestackbcdrconsiderationswp).

## <a name="the-infrastructure-backup-service"></a>Zálohovací služby infrastruktury

Služby obsahují následující funkce.

| Funkce                                            | Popis                                                                                                                                                |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Služby infrastruktury zálohování                     | Zálohování koordinaci mezi podmnožinou služby infrastruktury ve službě Azure Stack. Pokud dojde k havárii, je možné obnovit data v rámci opětovné nasazení. |
| Komprese a šifrování dat exportovaných zálohování | Zálohovaná data komprimovaná a šifrovaná systém předtím, než se exportují do umístění úložiště externí program od správce.                |
| Monitorování úlohy zálohování                              | Systém oznámení při selhání a nápravné kroky úlohy zálohování.                                                                                                |
| Prostředí správy zálohování                       | Zálohování RP podporuje povolení zálohování.                                                                                                                         |
| Cloud Recovery                                     | Pokud dojde ke ztrátě dat, zálohování umožňuje obnovit základní informace o službě Azure Stack jako součást nasazení.                                 |

## <a name="verify-requirements-for-the-infrastructure-backup-service"></a>Ověřte požadavky pro službu Backup infrastruktury

- **Umístění úložiště**  
  Budete potřebovat sdílené složky přístupné z Azure Stack, který může obsahovat sedm zálohy. Každá záloha je přibližně 10 GB. Vaše sdílené složky měli být schopni uložit 140 GB místa zálohy. Další informace o výběru umístění úložiště pro zálohy služby Azure Stack infrastruktury, najdete v části [záložní řadič požadavky](azure-stack-backup-reference.md#backup-controller-requirements).
- **Přihlašovací údaje**  
  Budete potřebovat účet uživatele domény a přihlašovací údaje, například můžete použít přihlašovací údaje Správce služby Azure Stack.
- **Šifrovací certifikát**  
  Záložní soubory jsou šifrované pomocí veřejného klíče v certifikátu. Ujistěte se, že se tento certifikát uložit na bezpečném místě. 


## <a name="next-steps"></a>Další postup

Zjistěte, jak [povolit zálohování pro Azure Stack z portálu pro správu](azure-stack-backup-enable-backup-console.md).

Zjistěte, jak [povolit zálohování pro Azure Stack s prostředím PowerShell](azure-stack-backup-enable-backup-powershell.md).

Zjistěte, jak [zálohování Azure stacku](azure-stack-backup-back-up-azure-stack.md )

Zjistěte, jak [obnovit ze ztráty dat](azure-stack-backup-recover-data.md)

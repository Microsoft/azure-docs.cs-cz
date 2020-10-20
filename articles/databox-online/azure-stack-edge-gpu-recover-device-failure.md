---
title: Obnovení ze selhání zařízení Azure Stack Edge pro
description: Popisuje, jak provést obnovení ze zařízení se selháním Azure Stack Edge pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 10/06/2020
ms.author: alkohli
ms.openlocfilehash: 91efdac9d17e679226f10012bbff9b5b6676ff0e
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2020
ms.locfileid: "92205574"
---
# <a name="recover-from-a-failed-azure-stack-edge-pro-gpu-device"></a>Obnovení z neúspěšného Azure Stackového zařízení pro grafické procesory 

Tento článek popisuje, jak provést obnovení z nepřípustného selhání na zařízení GPU Azure Stack Edge pro. Nepřípustná Chyba zařízení GPU Azure Stack Edge pro vyžaduje nahrazení zařízení.

## <a name="before-you-begin"></a>Než začnete

Ujistěte se, zda máte následující:

- Kontaktuje se podpora Microsoftu týkající se selhání zařízení a doporučuje se vyměnit zařízení. 
- Vaše konfigurace zařízení se zálohuje, jak je popsané v tématu [Příprava na selhání zařízení](azure-stack-edge-gpu-prepare-device-failure.md).


## <a name="configure-replacement-device"></a>Konfigurace náhradního zařízení

Když zařízení narazí na nepřípustné selhání, budete muset objednat náhradní zařízení. Konfigurační kroky pro náhradní zařízení zůstávají stejné. 

Načte informace o konfiguraci zařízení, které jste zálohovali ze zařízení, které selhalo. Tyto informace budete používat ke konfiguraci náhradního zařízení.  

Pomocí těchto kroků nakonfigurujete náhradní zařízení:

1. Shromážděte informace požadované v [kontrolním seznamu nasazení](azure-stack-edge-gpu-deploy-checklist.md). Měli byste použít informace, které jste uložili z předchozí konfigurace zařízení. 
1. Seřazení nového zařízení se stejnou konfigurací, které selhalo.  Chcete-li umístit objednávku, [vytvořte nový Azure Stack hraničního prostředku](azure-stack-edge-gpu-deploy-prep.md#) v Azure Portal.
1. [Rozbalením](azure-stack-edge-gpu-deploy-install.md#unpack-the-device), [připojení k racku](azure-stack-edge-gpu-deploy-install.md#rack-the-device) a [zapojení kabelů do zařízení](azure-stack-edge-gpu-deploy-install.md#cable-the-device). 
1. [Připojte se k místnímu uživatelskému rozhraní zařízení](azure-stack-edge-gpu-deploy-connect.md).
1. Nakonfigurujte síť pomocí stejných IP adres, které jste použili pro původní zařízení. Tím se minimalizuje dopad na všechny klientské počítače používané ve vašem prostředí. Podívejte se, jak [nakonfigurovat nastavení sítě](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).
1. Přiřaďte stejné jméno zařízení a doménu DNS jako staré zařízení. Tím zajistíte, že klienti budou moci používat stejný název zařízení pro komunikaci s novým zařízením. Viz jak [nakonfigurovat nastavení zařízení](azure-stack-edge-gpu-deploy-set-up-device-update-time.md).
1. Konfigurace certifikátů na novém zařízení stejným způsobem jako u starého zařízení. Mějte na paměti, že nové zařízení má nové sériové číslo uzlu. Pokud jste ve starém zařízení použili vlastní certifikáty, budete muset získat nový certifikát uzlu. Přečtěte si téma [Konfigurace certifikátů](azure-stack-edge-gpu-deploy-configure-certificates.md).
1. Získá aktivační klíč z Azure Portal a aktivuje nové zařízení. Podívejte se, jak [zařízení aktivovat](azure-stack-edge-gpu-deploy-activate.md).

Teď jste připraveni nasadit úlohy, které jste spustili na starém zařízení.

## <a name="restore-edge-cloud-shares"></a>Obnovení sdílených složek cloudu na hraničních zařízeních

Pomocí těchto kroků obnovte data na hraničních sdílených cloudech na vašem zařízení:

1. Do zařízení, které selhalo, [přidejte sdílené složky](azure-stack-edge-j-series-manage-shares.md#add-a-share) se stejnými názvy, které jste vytvořili dříve. Ujistěte se, že při vytváření sdílených složek je **Výběr kontejneru objektů BLOB** nastavený na **použití existující** možnosti a pak vyberte kontejner, který jste použili s předchozím zařízením.
1. [Přidejte uživatele](azure-stack-edge-j-series-manage-users.md#add-a-user) , kteří mají přístup k předchozímu zařízení.
1. [Přidejte účty úložiště](azure-stack-edge-j-series-manage-storage-accounts.md#add-an-edge-storage-account) přidružené ke sdíleným složkám dříve na zařízení. Při vytváření účtů úložiště Edge vyberte z existujícího kontejneru a najeďte na kontejner, který byl namapovaný na účet Azure Storage namapovaný na předchozím zařízení. Do vybraného kontejneru úložiště v namapovaném Azure Storage účtu se nahrála všechna data ze zařízení, která se zapsala do účtu úložiště Edge na předchozím zařízení.
1. [Aktualizujte sdílená](azure-stack-edge-j-series-manage-shares.md#refresh-shares) data z Azure. To by vyžadovalo stažení všech dat cloudu z existujícího kontejneru do sdílených složek.

## <a name="restore-edge-local-shares"></a>Obnovit místní sdílené složky Edge

Pro přípravu na možné selhání zařízení jste možná nasadili jedno z následujících řešení pro zálohování, které chrání místní sdílená data ze svých úloh Kubernetes nebo IoT:

| Software jiných výrobců           | Odkaz na řešení                               |
|--------------------------------|---------------------------------------------------------|
| Cohesity                       | [https://www.cohesity.com/solution/cloud/azure/](https://www.cohesity.com/solution/cloud/azure/) <br> Podrobnosti získáte od Cohesity.          |
| CommVault                      | https://www.commvault.com/azure <br> Podrobnosti získáte od CommVault. |
| Veritas                        | http://veritas.com/azure <br> Podrobnosti získáte od společnosti Veritas.   |

Po úplné konfiguraci náhradního zařízení povolte zařízení pro místní úložiště. 

Pomocí těchto kroků obnovte data z místních sdílených složek: 

1. [Proveďte konfiguraci výpočtů na zařízení](azure-stack-edge-gpu-deploy-configure-compute.md).
1. [Přidejte místní sdílení](azure-stack-edge-j-series-manage-shares.md#add-a-local-share) zpátky.
1. Spusťte postup obnovení poskytovaný řešením ochrany dat podle vlastního výběru. Viz odkazy z předchozí tabulky.

## <a name="restore-vm-files-and-folders"></a>Obnovení souborů a složek virtuálního počítače

Pokud chcete připravit na potenciální selhání zařízení, možná jste nasadili jedno z následujících řešení pro zálohování, která chrání data na virtuálních počítačích:



| Řešení zálohování        | Podporovaný operační systém   | Referenční informace                                                                |
|-------------------------|----------------|--------------------------------------------------------------------------|
| Agent Microsoft Azure Recovery Services (MARS) pro Azure Backup | Windows        | [Informace o agentovi Mars](/azure/backup/backup-azure-about-mars)    |
| Cohesity                | Windows, Linux | [Stručně Microsoft Azure integrace, řešení zálohování a obnovení](https://www.cohesity.com/solution/cloud/azure) <br>Podrobnosti získáte od Cohesity.                          |
| CommVault               | Windows, Linux | https://www.commvault.com/azure <br> Podrobnosti získáte od CommVault.
| Veritas                 | Windows, Linux | http://veritas.com/azure <br> Podrobnosti získáte od společnosti Veritas.                    |

Po úplné konfiguraci náhradního zařízení můžete virtuální počítače znovu nasadit pomocí dříve používané image virtuálního počítače. 

Pomocí těchto kroků obnovte data na virtuálních počítačích:
 
1. [Nasaďte virtuální počítač z image virtuálního počítače](azure-stack-edge-gpu-deploy-virtual-machine-templates.md) na zařízení. 
1. Nainstalujte na virtuální počítač řešení pro ochranu dat podle vlastního výběru.
1. Spusťte postup obnovení poskytovaný řešením ochrany dat podle vlastního výběru. Viz odkazy z předchozí tabulky.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [vrátit Azure Stack Edge pro zařízení](azure-stack-edge-return-device.md).

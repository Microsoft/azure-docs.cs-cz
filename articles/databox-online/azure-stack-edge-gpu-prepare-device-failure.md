---
title: Příprava pro selhání zařízení Azure Stack Edge pro
description: Popisuje, jak získat náhradu za zařízení s neúspěšnou Azure Stack Edge pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 60469dc834c28bd8dbc73a1883ea01b06797c01f
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2021
ms.locfileid: "102442974"
---
# <a name="prepare-for-an-azure-stack-edge-pro-gpu-device-failure"></a>Příprava na selhání zařízení s GRAFICKÝm rozhraním Azure Stack Edge pro

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

Tento článek vám pomůže připravit se na selhání zařízení tím, že podrobně popisuje, jak uložit a zálohovat konfiguraci zařízení a data na zařízení s grafickým procesorem Azure Stack Edge pro. 

Článek neobsahuje kroky pro zálohování Kubernetes a kontejnerů IoT nasazených na zařízení GPU pro Azure Stack Edge pro. 

## <a name="understand-device-failures"></a>Vysvětlení selhání zařízení

Zařízení GPU Azure Stack Edge pro může docházet k dvěma typům selhání hardwaru.

- Přípustné chyby, které vyžadují, abyste nahradili hardwarovou součást. Tyto chyby vám umožní pracovat se zařízením v neomezeném stavu. Mezi příklady těchto selhání patří jedna neúspěšná jednotka napájení (PSU) nebo jeden disk, který selhal v zařízení. V každém z těchto případů může zařízení fungovat i nadále. Pokud chcete nahradit neúspěšné součásti, kontaktujte podpora Microsoftu co nejdříve.

- Nepřípustné chyby, které vyžadují, abyste celé zařízení nahradili například v případě, že se na zařízení nezdařily dva disky. V těchto případech kontaktujte podpora Microsoftu hned. Po zjištění, že je potřeba zařízení vyměnit, vám podpora pomůže s náhradou zařízení Azure Stack Edge.

V rámci přípravy na nepřípustné chyby musíte na svém zařízení zálohovat následující věci:

- Informace o konfiguraci zařízení
- Data v hraničních místních sdílených složkách a sdílených cloudech na hraničních zařízeních
- Soubory a složky přidružené k virtuálním počítačům běžícím na vašem zařízení


## <a name="back-up-device-configuration"></a>Zálohování konfigurace zařízení

Při počáteční konfiguraci zařízení je důležité uchovávat kopii informací o konfiguraci zařízení, jak je uvedeno v [kontrolním seznamu nasazení](azure-stack-edge-gpu-deploy-checklist.md). Během obnovení se tyto informace o konfiguraci použijí pro nové náhradní zařízení. 

## <a name="protect-device-data"></a>Ochrana dat zařízení

Data zařízení mohou být jednoho z následujících typů:

- Data ve sdílených cloudech Edge
- Data v místních sdílených složkách
- Soubory a složky na virtuálních počítačích

Následující části popisují kroky a doporučení pro ochranu každého z těchto typů dat v zařízení.

## <a name="protect-data-in-edge-cloud-shares"></a>Ochrana dat ve sdílených cloudech Edge

Můžete vytvořit hraniční sdílení na úrovni cloudu, která budou mít data z vašeho zařízení do Azure. V závislosti na dostupné šířce pásma sítě nakonfigurujte na svém zařízení šablony šířky pásma, aby se minimalizovala ztráta dat, pokud dojde k nepřípustnému selhání.

> [!IMPORTANT]
> Pokud zařízení obsahuje neplatnou chybu, může dojít ke ztrátě místních dat, která nejsou ve vrstvách od zařízení do Azure. 

## <a name="protect-data-in-edge-local-shares"></a>Ochrana dat v místních sdílených složkách Edge

Pokud nasazujete Kubernetes nebo IoT Edge, nakonfigurujte aplikaci tak, aby se data aplikace místně ukládala na zařízení a nesynchronizovaná s Azure Storage. Data se ukládají do sdílené složky na zařízení. Může být důležité zálohovat data v těchto sdílených složkách.

Následující řešení ochrany dat třetích stran můžou poskytnout řešení zálohování pro data v místních sdílených složkách SMB nebo NFS. 

| Software jiných výrobců           | Odkaz na řešení                               |
|--------------------------------|---------------------------------------------------------|
| Cohesity                       | [https://www.cohesity.com/solution/cloud/azure/](https://www.cohesity.com/solution/cloud/azure/) <br> Podrobnosti získáte od Cohesity.          |
| CommVault                      | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br> Podrobnosti získáte od CommVault.          |
| Veritas                        | [http://veritas.com/azure](http://veritas.com/azure) <br> Podrobnosti získáte od společnosti Veritas.   |
| Veeam                          | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> Podrobnosti získáte od Veeam. |


## <a name="protect-files-and-folders-on-vms"></a>Ochrana souborů a složek na virtuálních počítačích

Azure Stack Edge spolupracuje s Azure Backup a dalšími řešeními ochrany dat třetích stran, které poskytuje řešení pro zálohování k ochraně dat obsažených v virtuálních počítačích nasazených na zařízení. V následující tabulce jsou uvedeny odkazy na dostupná řešení, ze kterých si můžete vybrat.


| Řešení zálohování        | Podporovaný operační systém   | Reference                                                                |
|-------------------------|----------------|--------------------------------------------------------------------------|
| Agent Microsoft Azure Recovery Services (MARS) pro Azure Backup | Windows        | [Informace o agentovi Mars](../backup/backup-azure-about-mars.md)    |
| Cohesity                | Windows, Linux | [Stručný Microsoft Azure integrace, řešení zálohování & obnovení](https://www.cohesity.com/solution/cloud/azure) <br>Podrobnosti získáte od Cohesity.                          |
| CommVault               | Windows, Linux | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br>Podrobnosti získáte od CommVault.                          |
| Veritas                 | Windows, Linux | [https://vox.veritas.com/t5/Protection/Protecting-Azure-Stack-Edge-with-NetBackup/ba-p/883370](https://vox.veritas.com/t5/Protection/Protecting-Azure-Stack-Edge-with-NetBackup/ba-p/883370) <br> Podrobnosti získáte od společnosti Veritas.                    |
| Veeam                   | Windows, Linux | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> Podrobnosti získáte od Veeam. |


## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak provést [obnovení z neúspěšného Azure Stackového zařízení pro grafické procesory](azure-stack-edge-gpu-recover-device-failure.md).
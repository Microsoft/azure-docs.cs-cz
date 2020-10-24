---
title: Dokončení zotavení po havárii virtuálních počítačů
description: Tento článek ukazuje, jak dokončit zotavení po havárii virtuálních počítačů pomocí řešení Azure VMware.
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 3608243600eb5d00dcfe10db5bc6b907ecb9aee8
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92508430"
---
# <a name="complete-a-disaster-recovery-of-virtual-machines-using-azure-vmware-solution"></a>Dokončení zotavení po havárii virtuálních počítačů pomocí řešení Azure VMware

Tento článek obsahuje proces dokončení zotavení po havárii virtuálních počítačů pomocí řešení VMware HCX a použití privátního cloudu řešení Azure VMware jako lokality pro obnovení nebo cílovou lokalitu.

VMware HCX poskytuje různé operace, které poskytují přesné řízení a členitost zásad replikace. K dispozici jsou tyto operace:

- **Reverse** – po havárii došlo k chybě. Reverse umožňuje vytvořit lokalitu B zdrojový web a lokalitu, kde je nyní chráněný virtuální počítač.

- **Pozastavit** – pozastaví aktuální zásadu replikace přidruženou k VYBRANÉmu virtuálnímu počítači.

- **Pokračovat** – obnoví aktuální zásady replikace přidružené k VYBRANÉmu virtuálnímu počítači.

- **Odebrat** – Odebere aktuální zásadu replikace přidruženou k VYBRANÉmu virtuálnímu počítači.

- **Synchronizovat hned** – z vázaného virtuálního počítače se zdrojem synchronizace s CHRÁNĚNým virtuálním počítačem.

V této příručce jsou pokryty tyto scénáře replikace:

- Chraňte virtuální počítač nebo skupinu virtuálních počítačů.

- Proveďte test obnovení virtuálního počítače nebo skupiny virtuálních počítačů.

- Obnovte virtuální počítač nebo skupinu virtuálních počítačů.

- Reverzní ochrana virtuálního počítače nebo skupiny virtuálních počítačů.

## <a name="protect-vms"></a>Ochrana virtuálních počítačů

1. Přihlaste se ke **klientovi vSphere** ve zdrojové lokalitě a přístup k **modulu plug-in HCX**.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/hcx-vsphere.png" alt-text="Možnost HCX v vSphere" border="true":::

1. Zadejte oblast **zotavení po havárii** a vyberte **chránit virtuální počítače**.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-virtual-machine.png" alt-text="Možnost HCX v vSphere" border="true" lightbox="./media/disaster-recovery-virtual-machines/protect-virtual-machine.png":::

1. Vyberte zdroj a vzdálené lokality. Vzdálená lokalita v tomto případě by měla být privátní cloud řešení Azure VMware.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-virtual-machines.png" alt-text="Možnost HCX v vSphere" border="true":::

1. V případě potřeby vyberte **výchozí možnosti replikace** :

   - **Povolit kompresi:** Doporučuje se pro scénáře s nízkou propustností.

   - **Povolit Quiescence:** Pozastaví virtuální počítač, aby se zajistila synchronizace konzistentní kopie na vzdálené lokalitě.

   - **Cílové úložiště:** Vzdálené úložiště dat pro chráněné virtuální počítače a v privátním cloudu řešení VMware Azure by mělo být úložiště dat síti vSAN.

   - **Kontejner Compute:** Vzdálený cluster vSphere nebo fond zdrojů.

   - **Cílová složka:** Složka pro vzdálenou cílovou složku, která je volitelná a pokud není vybrána žádná složka, virtuální počítače budou umístěny přímo pod vybraným clusterem.

   - **Cíl** bodu obnovení: Interval synchronizace mezi zdrojovým virtuálním počítačem a chráněným virtuálním počítačem a může být odkudkoli od 5 minut do 24 hodin.

   - **Interval snímku:** Interval mezi snímky

   - **Počet snímků:** Celkový počet snímků v rámci nakonfigurovaného intervalu snímků.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-virtual-machine-options.png" alt-text="Možnost HCX v vSphere" border="true" lightbox="./media/disaster-recovery-virtual-machines/protect-virtual-machine-options.png":::

1. V seznamu vyberte jeden nebo víc virtuálních počítačů a podle potřeby nakonfigurujte možnosti replikace.

   Virtuální počítače ve výchozím nastavení dědí zásadu globálního nastavení nakonfigurovanou v možnostech výchozí replikace. Pro každé síťové rozhraní ve vybraném virtuálním počítači nakonfigurujte **skupinu portů vzdálené sítě** a kliknutím na **Dokončit** zahajte proces ochrany.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/network-interface-options.png" alt-text="Možnost HCX v vSphere" border="true" lightbox="./media/disaster-recovery-virtual-machines/network-interface-options.png":::

1. Monitorujte proces pro všechny vybrané virtuální počítače ve stejné oblasti zotavení po havárii.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-monitor-progress.png" alt-text="Možnost HCX v vSphere" border="true" lightbox="./media/disaster-recovery-virtual-machines/protect-monitor-progress.png":::

1. Po ochraně virtuálního počítače můžete zobrazit různé snímky na kartě **snímky** .

   :::image type="content" source="./media/disaster-recovery-virtual-machines/list-of-snapshots.png" alt-text="Možnost HCX v vSphere" border="true" lightbox="./media/disaster-recovery-virtual-machines/list-of-snapshots.png":::

   Žlutý trojúhelník znamená, že se snímky a virtuální testy netestovaly v operaci obnovení testu.

   Existují klíčové rozdíly mezi virtuálním počítačem, který je vypnutý, a jedním z nich. Na obrázku je znázorněn proces synchronizace pro virtuální počítač s výkonem. Proces synchronizace se spustí, dokud dokončí první snímek, což je úplná kopie virtuálního počítače a pak dokončí další v nakonfigurovaném intervalu. Pro vypnutý virtuální počítač synchronizuje kopii a pak se virtuální počítač zobrazí jako neaktivní a operace ochrany se zobrazí jako dokončené.  Když je virtuální počítač zapnutý, spustí synchronizaci procesu se vzdáleným webem.

## <a name="complete-a-test-recover-of-vms"></a>Dokončit test obnovení virtuálních počítačů

1. Přihlaste se ke **klientovi vSphere** ve vzdálené lokalitě, což je privátní cloud řešení Azure VMware. 
1. V rámci **modulu plug-in HCX**v oblasti zotavení po havárii vyberte svislé tři tečky na libovolném virtuálním počítači, aby se zobrazila nabídka operace, a pak vyberte **test obnovení virtuálního počítače**.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/test-recover-virtual-machine.png" alt-text="Možnost HCX v vSphere" border="true":::

1. Vyberte možnosti pro test a snímek, který chcete použít k otestování různých stavů virtuálního počítače.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/choose-snapshot.png" alt-text="Možnost HCX v vSphere" border="true":::

1. Po výběru **testu**začíná operace obnovení.

1. Po dokončení můžete nový virtuální počítač vyzkoušet v rámci úložiště Azure VMware privátního cloudu vCenter.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/verify-test-recovery.png" alt-text="Možnost HCX v vSphere" border="true" lightbox="./media/disaster-recovery-virtual-machines/verify-test-recovery.png":::

1. Po provedení testování na virtuálním počítači nebo v jakékoli aplikaci, která je na něm běží, proveďte vyčištění, aby se odstranila instance testu.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/cleanup-test-instance.png" alt-text="Možnost HCX v vSphere" border="true" lightbox="./media/disaster-recovery-virtual-machines/cleanup-test-instance.png":::

## <a name="recover-vms"></a>Obnovení virtuálních počítačů

1. Přihlaste se ke **klientovi vSphere** ve vzdálené lokalitě, která je privátním cloudem řešení Azure VMware a získejte přístup k **modulu plug-in HCX**.

   Pro scénář obnovení se jedná o skupinu virtuálních počítačů použitých v tomto příkladu.

1. Vyberte virtuální počítač, který se má obnovit ze seznamu, otevřete nabídku **Akce** a vyberte **obnovení virtuálních počítačů**.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/recover-virtual-machines.png" alt-text="Možnost HCX v vSphere" border="true":::

1. Nakonfigurujte možnosti obnovení pro jednotlivé instance a výběrem možnosti **obnovit** spusťte operaci obnovení.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/recover-virtual-machines-confirm.png" alt-text="Možnost HCX v vSphere" border="true":::

1. Po dokončení operace obnovení se nové virtuální počítače objeví v inventáři vzdálené vCenter Server.

## <a name="complete-a-reverse-replication-on-vms"></a>Dokončení zpětné replikace na virtuálních počítačích

1. Přihlaste se ke **klientovi vSphere** v privátním cloudu řešení Azure VMware a získejte přístup k **modulu plug-in HCX**.
   
   >[!NOTE]
   >Než začnete zpětnou replikaci, ujistěte se, že původní virtuální počítače ve zdrojové lokalitě jsou vypnuté. Operace se nezdařila, pokud virtuální počítače nejsou vypnuté.

1. V seznamu vyberte virtuální počítače, které chcete replikovat zpět do zdrojové lokality, otevřete nabídku **Akce** a vyberte možnost **obrátit**. 
1. Kliknutím na tlačítko **zpět** spusťte replikaci.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/reverse-operation-virtual-machines.png" alt-text="Možnost HCX v vSphere" border="true":::

1. Monitorujte v části Podrobnosti každého virtuálního počítače.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/review-reverse-operation.png" alt-text="Možnost HCX v vSphere" border="true" lightbox="./media/disaster-recovery-virtual-machines/review-reverse-operation.png":::

## <a name="disaster-recovery-plan-automation"></a>Automatizace plánu zotavení po havárii

VMware HCX aktuálně nemá integrovaný mechanizmus pro vytvoření a automatizaci plánu zotavení po havárii. VMware HCX však poskytuje sadu rozhraní REST API, včetně rozhraní API pro operaci zotavení po havárii. Specifikace rozhraní API je k dispozici v rámci VMware HCX Manageru v adrese URL.

Tato rozhraní API jsou popsaná v následujících činnostech v oblasti zotavení po havárii.

- Ochrana

- Zotavit

- Obnovit test

- Plánované obnovení

- Reverse

- Dotaz

- Vyčištění testu

- Pozastavení

- Obnovit

- Odebrat ochranu

- Změna konfigurace

Příklad datové části operace obnovení ve formátu JSON je uveden níže.

```json
[

    {

        "replicationId": "string",

        "needPowerOn": true,

        "instanceId": "string",

        "source": {

            "endpointType": "string",

            "endpointId": "string",

            "endpointName": "string",

            "resourceType": "string",

            "resourceId": "string",

            "resourceName": "string"

        },

        "destination": {

            "endpointType": "string",

            "endpointId": "string",

            "endpointName": "string",

            "resourceType": "string",

            "resourceId": "string",

            "resourceName": "string"

        },

        "placement": [

            {

                "containerType": "string",

                "containerId": "string"

            }

        ],

        "resourceId": "string",

        "forcePowerOff": true,

        "isTest": true,

        "forcePowerOffAfterTimeout": true,

        "isPlanned": true

    }

]
```

Pomocí těchto rozhraní API může zákazník vytvořit vlastní mechanismus pro automatizaci vytváření a provádění plánu zotavení po havárii.

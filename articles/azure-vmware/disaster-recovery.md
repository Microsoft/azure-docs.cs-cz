---
title: Dokončení zotavení po havárii virtuálních počítačů
description: Tento článek ukazuje, jak dokončit zotavení po havárii virtuálních počítačů pomocí řešení Azure VMware.
ms.topic: how-to
ms.date: 05/04/2020
ms.openlocfilehash: 76a417b9ba00c4c0e6e958e5a04d19aecfe24563
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2020
ms.locfileid: "88752265"
---
# <a name="complete-a-disaster-recovery-of-virtual-machines-using-azure-vmware-solution"></a>Dokončení zotavení po havárii virtuálních počítačů pomocí řešení Azure VMware

Tento článek obsahuje proces dokončení zotavení po havárii virtuálních počítačů pomocí řešení HCX (VMware Hybrid Cloud Extension) a použití privátního cloudu řešení Azure VMware jako lokality pro obnovení nebo cílovou lokalitu.

VMware HCX poskytuje různé operace, které poskytují přesné řízení a členitost zásad replikace. K dispozici jsou tyto operace:

- Reverse – po havárii došlo k chybě. Reverse umožňuje vytvořit lokalitu B zdrojový web a lokalitu, kde je nyní chráněný virtuální počítač.

- Pozastavit – pozastaví aktuální zásadu replikace přidruženou k vybranému virtuálnímu počítači.

- Obnovit – pozastavit aktuální zásadu replikace přidruženou k vybranému virtuálnímu počítači.

- Odebrat – Odebere aktuální zásadu replikace přidruženou k vybranému virtuálnímu počítači.

- Synchronizovat nyní – z vázaného virtuálního počítače se zdrojem synchronizace s chráněným virtuálním počítačem.

V této příručce jsou pokryty tyto scénáře replikace:

- Chraňte virtuální počítač nebo skupinu virtuálních počítačů.

- Proveďte test obnovení virtuálního počítače nebo skupiny virtuálních počítačů.

- Obnovte virtuální počítač nebo skupinu virtuálních počítačů.

- Reverzní ochrana virtuálního počítače nebo skupiny virtuálních počítačů.

## <a name="protect-virtual-machines"></a>Ochrana virtuálních počítačů

Přihlaste se ke **klientovi vSphere** ve zdrojové lokalitě a přístup k **modulu plug-in HCX**.

:::image type="content" source="./media/disaster-recovery/hcx-vsphere.png" alt-text="Možnost HCX v vSphere" border="true":::

Zadejte oblast **zotavení po havárii** a klikněte na **chránit virtuální počítače**.

:::image type="content" source="./media/disaster-recovery/protect-virtual-machine.png" alt-text="Výběr chránit virtuální počítače" border="true":::

V okně, které se otevře, vyberte zdroj a vzdálené lokality, vzdálený web v tomto případě by měl být privátní cloud řešení Azure VMware.

:::image type="content" source="./media/disaster-recovery/protect-virtual-machines.png" alt-text="okno pro ochranu virtuálních počítačů" border="true":::

V případě potřeby vyberte výchozí možnosti replikace:

- **Povolit kompresi:** Doporučuje se pro scénáře s nízkou propustností.

- **Povolit Quiescence:** Pozastaví virtuální počítač, aby se zajistila synchronizace konzistentní kopie na vzdálené lokalitě.

- **Cílové úložiště:** Vyberte vzdálené úložiště dat pro chráněné virtuální počítače. V privátním cloudu řešení VMware Azure by měl být tento výběr úložiště dat síti vSAN.

- **Kontejner Compute:** Vzdálený cluster vSphere nebo fond zdrojů.

- **Cílová složka:** Složka vzdáleného cíle, toto nastavení je volitelné, a pokud není vybrána žádná složka, virtuální počítače budou tempy přímo pod vybraným clusterem.

- **Cíl** bodu obnovení: Tato hodnota je interval synchronizace mezi zdrojovým virtuálním počítačem a chráněným virtuálním počítačem a může být odkudkoli od 5 minut do 24 hodin.

- **Interval snímku:** Interval mezi snímky

- **Počet snímků:** Celkový počet snímků v rámci nakonfigurovaného intervalu snímků.

:::image type="content" source="./media/disaster-recovery/protect-virtual-machine-options.png" alt-text="možnosti ochrany virtuálních počítačů" border="true":::

V seznamu vyberte jeden nebo víc virtuálních počítačů a podle potřeby nakonfigurujte možnosti replikace v rámci virtuálního počítače.

Ve výchozím nastavení zdědí virtuální počítače zásadu globálního nastavení nakonfigurovanou ve výchozích možnostech replikace. Pro každé síťové rozhraní ve vybraném virtuálním počítači nakonfigurujte **skupinu portů vzdálené sítě** a kliknutím na **Dokončit** zahajte proces ochrany.

:::image type="content" source="./media/disaster-recovery/network-interface-options.png" alt-text="možnosti síťového rozhraní" border="true":::

Jak je vidět na následujícím obrázku, můžete sledovat proces pro každý z vybraných virtuálních počítačů ve stejné oblasti zotavení po havárii.

:::image type="content" source="./media/disaster-recovery/protect-monitor-progress.png" alt-text="monitorování průběhu ochrany" border="true":::

Po ochraně virtuálního počítače se na kartě **snímky** mohou zobrazit různé snímky.

:::image type="content" source="./media/disaster-recovery/list-of-snapshots.png" alt-text="seznam snímků" border="true":::

Žlutý trojúhelník znamená, že se snímky a virtuální testy netestovaly v operaci obnovení testu.

Existují klíčové rozdíly mezi virtuálním počítačem, který je vypnutý, a jedním z nich.
Předchozí snímek obrazovky ukazuje proces synchronizace pro technologii na virtuálním počítači. Proces synchronizace začal, dokud nekončí prvním snímkem, což je úplná kopie virtuálního počítače, a pak dokončí další v nakonfigurovaném intervalu.

Pro vypínání virtuálního počítače se synchronizuje kopie a pak se virtuální počítač zobrazí jako neaktivní a operace ochrany se zobrazí jako dokončené.

Když je virtuální počítač zapnutý, spustí se synchronizace procesu na vzdálené lokalitě.

## <a name="complete-a-test-recover-of-virtual-machines"></a>Dokončit test obnovení virtuálních počítačů

Přihlaste se ke **klientovi vSphere** ve vzdálené lokalitě, což je privátní cloud řešení Azure VMware. V rámci **modulu plug-in HCX**v oblasti zotavení po havárii vyberte svislé tři tečky na libovolném virtuálním počítači, aby se zobrazila nabídka operace. Vyberte **test obnovit virtuální počítač**.

:::image type="content" source="./media/disaster-recovery/test-recover-virtual-machine.png" alt-text="Vybrat test obnovit virtuální počítač" border="true":::

V novém okně vyberte možnosti pro test. Vyberte snímek, který chcete použít k testování různých stavů virtuálního počítače.

:::image type="content" source="./media/disaster-recovery/choose-snapshot.png" alt-text="zvolit snímek a kliknout na test" border="true":::

Po kliknutí na **test**bude zahájena operace obnovení.

Po dokončení operace obnovení testovacího prostředí se dá nový virtuální počítač vrátit do úložiště Azure VMware privátního cloudu vCenter.

:::image type="content" source="./media/disaster-recovery/verify-test-recovery.png" alt-text="ověřit operaci obnovení" border="true":::

Nakonec po otestování na virtuálním počítači nebo v jakékoli aplikaci, která je na něm běží, proveďte vyčištění, aby se odstranila instance testu.

:::image type="content" source="./media/disaster-recovery/cleanup-test-instance.png" alt-text="instance testu vyčištění" border="true":::

## <a name="recover-virtual-machines"></a>Obnovení virtuálních počítačů

Přihlaste se ke **klientovi vSphere** ve vzdálené lokalitě, která je privátním cloudem řešení Azure VMware a získejte přístup k **modulu plug-in HCX**.

V případě scénáře obnovení je skupina virtuálních počítačů použitá v tomto příkladu.

V seznamu vyberte virtuální počítač, který se má obnovit, otevřete nabídku **Akce** a vyberte **obnovení virtuálních počítačů**.

:::image type="content" source="./media/disaster-recovery/recover-virtual-machines.png" alt-text="obnovení virtuálních počítačů" border="true":::

Nakonfigurujte možnosti obnovení pro každou instanci a kliknutím na **obnovit** spusťte operaci obnovení.

:::image type="content" source="./media/disaster-recovery/recover-virtual-machines-confirm.png" alt-text="potvrzení obnovení virtuálních počítačů" border="true":::

Po dokončení operace obnovení se nové virtuální počítače zobrazí v inventáři vzdálené vCenter Server.

## <a name="complete-a-reverse-replication-on-virtual-machines"></a>Dokončení zpětné replikace na virtuálních počítačích

Přihlaste se ke **klientovi vSphere** v privátním cloudu řešení Azure VMware a získejte přístup k **modulu plug-in HCX**.
Před zahájením zpětné replikace je nutné, aby původní virtuální počítače ve zdrojové lokalitě byly vypnuté. Tato operace se nezdařila, pokud nejsou virtuální počítače vypnuty.

V seznamu vyberte virtuální počítače, které mají být replikovány zpátky do zdrojové lokality, otevřete nabídku **Akce** a vyberte možnost **obrátit**. V automaticky otevíraném okně kliknutím na tlačítko **zpět** spusťte replikaci.

:::image type="content" source="./media/disaster-recovery/reverse-operation-virtual-machines.png" alt-text="V části chránit operace vyberte možnost obrátit akci." border="true":::

Replikaci můžete monitorovat v části Podrobnosti každého virtuálního počítače.

:::image type="content" source="./media/disaster-recovery/review-reverse-operation.png" alt-text="Kontrola výsledků zpětné akce" border="true":::

## <a name="disaster-recovery-plan-automation"></a>Automatizace plánu zotavení po havárii

VMware HCX aktuálně nemá integrovaný mechanizmus pro vytvoření a automatizaci plánu zotavení po havárii. Tato funkce v HCX neexistuje. Poskytuje ale sadu rozhraní REST API, včetně rozhraní API pro operaci zotavení po havárii.

Specifikace rozhraní API může být přístupná v rámci HCX Manageru v adrese URL.

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

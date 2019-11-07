---
title: Seskupení počítačů v Azure Migrate s využitím vizualizace závislosti bez agentů
description: Popisuje, jak vytvořit skupiny pomocí závislostí počítačů bez agentů.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 10/23/2019
ms.author: hamusa
ms.openlocfilehash: e9f9e812d5463f0a503b100780f9b988e43f748d
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720269"
---
# <a name="set-up-agentless-dependency-visualization-for-assessment"></a>Nastavení Vizualizace závislostí bez agentů pro posouzení

> [!NOTE]
> Pokud tuto funkci na portále Azure Migrate ještě nevidíte, počkejte prosím. Zobrazí se za příští týden.

Tento článek popisuje, jak nastavit mapování závislostí bez agentů v Azure Migrate: posouzení serveru. Tato funkce je v současnosti k dispozici ve verzi Preview pro počítače VMware zjištěné pomocí zařízení Azure Migrate. 

> [!IMPORTANT]
> Vizualizace závislostí bez agentů je momentálně ve verzi Preview pro virtuální počítače Azure VMware zjištěné pomocí zařízení Azure Migrate.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="about-dependency-mapping"></a>O mapování závislostí 

Mapování závislostí pomáhá vizualizovat závislosti mezi počítači, které chcete vyhodnotit a migrovat. Mapování závislostí se obvykle používá, pokud chcete vyhodnotit počítače s vyšší úrovní spolehlivosti.

- V Azure Migrate: posouzení serveru shromažďujete počítače dohromady do skupin pro posouzení. Skupiny se obvykle skládají z počítačů, které chcete migrovat společně, a mapování závislostí vám pomůže kontrolovat závislosti počítačů, abyste mohli počítače seskupit přesně.
- Pomocí mapování můžete zjistit vzájemně závislé systémy, které potřebují migrovat dohromady. Můžete také zjistit, zda běžící systém stále obsluhuje uživatele, nebo je kandidátem na vyřazení z provozu místo migrace.
- Vizualizace závislostí pomáhá zajistit, že nic není na pozadí, a během migrace se vyhnete nepřekročení výpadků.

## <a name="about-agentless-visualization"></a>Vizualizace bez agentů

Vizualizace závislostí bez agentů nevyžaduje, abyste nainstalovali žádné agenty na počítače. Funguje tak, že zachytává data připojení TCP z počítačů, pro které je povolená.

- Po spuštění zjišťování závislostí zařízení shromáždí data z počítačů v intervalu dotazování po dobu pěti minut.
- Shromažďují se následující data:
    - Připojení TCP
    - Názvy procesů, které mají aktivní připojení
    - Názvy nainstalovaných aplikací, které spouštějí výše uvedené procesy
    - Ne. připojení zjištěných při každém intervalu dotazování

## <a name="current-limitations"></a>Aktuální omezení

- Vizualizace závislostí bez agentů je aktuálně dostupná jenom pro virtuální počítače VMware.
- Teď nemůžete přidat nebo odebrat server ze skupiny v zobrazení analýzy závislostí. 
- Mapování závislostí pro skupinu serverů není aktuálně k dispozici.
- V současné době nelze data závislostí stáhnout v tabulkovém formátu.

## <a name="before-you-start"></a>Než začnete

- Ujistěte se, že jste [vytvořili](how-to-add-tool-first-time.md) projekt Azure Migrate.
- Analýza závislostí bez agentů je aktuálně dostupná jenom pro počítače VMware.
- Pokud jste již vytvořili projekt, ujistěte se, že jste [přidali](how-to-assess.md) Azure Migrate: nástroj Server Assessment Tool.
- Ujistěte se, že jste počítače VMware zjistili v Azure Migrate. to můžete provést nastavením zařízení Azure Migrate pro [VMware](how-to-set-up-appliance-vmware.md). Zařízení zjišťuje místní počítače a odesílá data o metadatech a výkonu Azure Migrate: posouzení serveru. [Další informace](migrate-appliance.md)
- Zajistěte, aby byly virtuální počítače VMware podporované pro vizualizaci závislostí bez agentů, jak je shrnuto v následující tabulce.


### <a name="supported-operating-systems"></a>Podporované operační systémy
 
Podporované operační systémy pro vizualizaci závislostí bez agentů jsou následující.

**Typ** | **Podporované operační systémy**
--- | --- 
**Windows** | Microsoft Windows Server 2016 <br/> Microsoft Windows Server 2012 R2 <br/> Microsoft Windows Server 2012 <br/> Microsoft Windows Server 2008 R2 (64 bitů) 
**Linux** | Red Hat Enterprise Linux 7, 6, 5 <br/> Ubuntu Linux 14,04, 16,04 <br/> Debian 7, 8 <br/> Oracle Linux 6, 7 <br/> CentOS 5, 6, 7  


## <a name="create-a-user-account-for-discovery"></a>Vytvoření uživatelského účtu pro zjišťování

Nastavte uživatelský účet, který má požadovaná oprávnění, aby vyhodnocování serveru mělo přístup k virtuálnímu počítači pro zjišťování. Můžete zadat jeden uživatelský účet.

- **Požadovaná oprávnění na virtuálních počítačích s Windows**: uživatelský účet vyžaduje přístup hosta.
- **Požadovaná oprávnění pro virtuální počítače se systémem Linux**: pro účet je vyžadováno oprávnění root. Tento uživatelský účet také vyžaduje tyto dvě funkce na/bin/netstat a/bin/ls: CAP_DAC_READ_SEARCH a CAP_SYS_PTRACE.

## <a name="add-the-user-account-to-the-appliance"></a>Přidat uživatelský účet do zařízení

Je nutné přidat uživatelský účet do zařízení.

Přidejte účet následujícím způsobem:

1. Otevřete aplikaci pro správu zařízení. Přejděte na panel **poskytnout podrobnosti vCenter** .
2. V části **Vyhledat aplikaci a závislosti na virtuálních počítačích** klikněte na **Přidat přihlašovací údaje** .
3. Vyberte **operační systém**. 
4. Zadejte popisný název účtu.
5. Zadejte **uživatelské jméno** a **heslo** .
6. Klikněte na **Uložit**.
7. Klikněte na **Uložit a spusťte zjišťování**.

    ![Přidat uživatelský účet VM](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)

## <a name="start-dependency-discovery"></a>Spustit zjišťování závislosti

Vyberte počítače, u kterých chcete povolit zjišťování závislostí.

1. V **Azure Migrate: vyhodnocování serveru**klikněte na **zjištěné servery**.
2. Klikněte na ikonu **Analýza závislostí** .
3. Klikněte na **Spustit zjišťování závislostí**.
3. Na stránce **zahájit zjišťování závislosti** vyberte zařízení, které zjišťuje příslušné počítače.
4. V seznamu počítač vyberte počítače.
5. Klikněte na **Spustit zjišťování závislostí**.

    ![Spustit zjišťování závislosti](./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png)

Po spuštění zjišťování závislostí budete moci vizualizovat závislosti 6 hodin.

## <a name="visualize-dependencies"></a>Vizualizace závislostí

1. V **Azure Migrate: vyhodnocování serveru**klikněte na **zjištěné servery**.
2. Vyhledejte počítač, pro který chcete zobrazit mapu závislostí.
3. Klikněte na **Zobrazit závislosti** ve sloupci **závislosti** .
4. Změňte časové období, pro které chcete zobrazit mapu pomocí rozevíracího seznamu doba **Trvání** .
5. Rozbalte skupinu **klientů** , aby vybrala seznam počítačů, které mají závislost na vybraném počítači. 
6. Rozbalte skupinu **portů** a seznam počítačů, které mají závislost z vybraného počítače. 
7. Chcete-li přejít k zobrazení mapy libovolného závislého počítače, klikněte na název počítače a potom klikněte na možnost **Načíst mapu serveru** .

    ![Rozbalte položku Skupina portů serveru a mapa zátěžového serveru.](./media/how-to-create-group-machine-dependencies-agentless/load-server-map.png)

    ![Rozbalit skupinu klientů ](./media/how-to-create-group-machine-dependencies-agentless/expand-client-group.png)

8. Rozbalte vybraný počítač pro zobrazení podrobností na úrovni procesu pro každou závislost.

    ![Rozbalte možnost Server a zobrazte procesy.](./media/how-to-create-group-machine-dependencies-agentless/expand-server-processes.png)

> [!NOTE]
> Informace o procesu pro závislost není vždy k dispozici. Pokud není k dispozici, závislost je znázorněna s procesem označeným jako "Neznámý proces".

## <a name="stop-dependency-discovery"></a>Zastavit zjišťování závislosti

Vyberte počítače, u kterých chcete zastavit zjišťování závislostí.

1. V **Azure Migrate: vyhodnocování serveru**klikněte na **zjištěné servery**.
2. Klikněte na ikonu **Analýza závislostí** .
3. Klikněte na **zastavit zjišťování závislostí**.
3. Na stránce **zastavení zjišťování závislostí** vyberte **zařízení** , které zjišťuje virtuální počítače, na kterých jste se vyhledali, aby se zjišťování závislostí zastavilo.
4. V seznamu počítač vyberte počítače.
5. Klikněte na **zastavit zjišťování závislostí** .


## <a name="next-steps"></a>Další kroky

[Seskupení počítačů](how-to-create-a-group.md)

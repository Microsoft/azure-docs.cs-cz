---
title: Povolení replikace pro místní počítače s privátními koncovými body
description: Tento článek popisuje, jak nakonfigurovat replikaci pro místní počítače pomocí privátních koncových bodů v Site Recovery.
author: Harsha-CS
ms.author: harshacs
ms.service: site-recovery
ms.topic: article
ms.date: 07/14/2020
ms.openlocfilehash: 3d15f4039da85dfa926e7bc9ab96b2c48965d5f0
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89658806"
---
# <a name="replicate-on-premises-machines-by-using-private-endpoints"></a>Replikace místních počítačů pomocí privátních koncových bodů

Azure Site Recovery umožňuje používat privátní koncové body [privátního propojení Azure](../private-link/private-endpoint-overview.md) k replikaci místních počítačů do virtuální sítě v Azure. Přístup privátního koncového bodu k trezoru obnovení se podporuje ve všech oblastech pro státní správu Azure Commercial &.

Tento článek popisuje, jak provést následující kroky:

- Vytvořte Azure Backup Recovery Services trezor pro ochranu vašich počítačů.
- Povolte pro trezor spravovanou identitu. Udělte oprávnění požadovaná pro přístup k účtům úložiště, aby bylo možné povolit replikaci provozu z místních do cílových umístění Azure. Přístup ke spravované identitě pro úložiště je nutný k přístupu privátního odkazu k trezoru.

- Proveďte změny DNS, které jsou požadovány pro soukromé koncové body.
- Vytváření a schvalování privátních koncových bodů pro trezor uvnitř virtuální sítě.
- Vytvořte privátní koncové body pro účty úložiště. V případě potřeby můžete pro úložiště dál povolený veřejný přístup nebo přístup přes bránu firewall. Vytvoření privátního koncového bodu pro přístup do úložiště není pro Azure Site Recovery nutné.
  
Následující diagram znázorňuje pracovní postup replikace hybridního zotavení po havárii s privátními koncovými body. V místní síti nemůžete vytvořit privátní koncové body. Pokud chcete používat privátní odkazy, musíte v tomto článku vytvořit virtuální síť Azure (označovanou jako *obejít síť* ), vytvořit privátní připojení mezi místními počítači a obejít síť a pak vytvořit privátní koncové body v síti s obejít. Můžete zvolit libovolnou formu privátního připojení.

:::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/architecture.png" alt-text="Diagram znázorňující architekturu Azure Site Recovery a soukromých koncových bodů.":::

## <a name="prerequisites-and-caveats"></a>Požadavky a upozornění

- Privátní odkazy jsou podporované v Site Recovery 9,35 a novějších.
- Můžete vytvořit soukromé koncové body pouze pro nové trezory Recovery Services, u kterých nejsou registrovány žádné položky. Proto je nutné vytvořit privátní koncové body před přidáním položek do trezoru. Informace o cenách najdete v tématu [ceny soukromého propojení Azure](https://azure.microsoft.com/pricing/details/private-link/) .
- Když vytvoříte privátní koncový bod pro trezor, trezor se zamkne. Dá se použít jenom v sítích, které mají privátní koncové body.
- Azure Active Directory v současné době nepodporuje privátní koncové body. Proto je potřeba umožnit odchozí přístup z zabezpečené virtuální sítě Azure na IP adresy a plně kvalifikované názvy domén, které jsou nutné, aby Azure Active Directory fungovaly v určité oblasti.
  V případě potřeby můžete také použít značky skupiny zabezpečení sítě Azure Active Directory a Azure Firewall, abyste povolili přístup k Azure Active Directory.
- V síti pro obejít, kde vytvoříte privátní koncový bod, se vyžaduje pět IP adres. Když vytvoříte privátní koncový bod pro trezor, Site Recovery vytvoří pět privátních odkazů pro přístup ke svým mikroslužbám.
- Pro připojení privátního koncového bodu k účtu úložiště mezipaměti se vyžaduje jedna další IP adresa v síti bypass. Můžete použít libovolnou metodu připojení mezi místním serverem a koncovým bodem účtu úložiště. Můžete například použít Internet nebo Azure [ExpressRoute](../expressroute/index.yml). Vytvoření privátního odkazu je volitelné. Soukromé koncové body pro úložiště můžete vytvořit pouze v účtech Pro obecné účely v2. V tématu [ceny objektů blob stránky Azure](https://azure.microsoft.com/pricing/details/storage/page-blobs/) najdete informace o cenách přenosu dat na účty pro obecné účely v2.

 ## <a name="create-and-use-private-endpoints-for-site-recovery"></a>Vytvoření a použití privátních koncových bodů pro Site Recovery

 V následujících částech najdete popis kroků, které je třeba provést při vytváření a používání privátních koncových bodů pro Site Recovery ve vašich virtuálních sítích.

> [!NOTE]
> Doporučujeme, abyste provedli následující kroky v uvedeném pořadí. Pokud to neuděláte, možná nebudete moci používat privátní koncové body v trezoru a může se stát, že budete muset proces restartovat s novým trezorem.

### <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

Recovery Services trezor obsahuje informace o replikaci počítačů. Slouží ke spuštění operací Site Recovery. Informace o tom, jak vytvořit trezor Recovery Services v oblasti Azure, ve které chcete převzít služby po havárii, najdete v tématu [Vytvoření trezoru Recovery Services](./azure-to-azure-tutorial-enable-replication.md#create-a-recovery-services-vault).

### <a name="enable-the-managed-identity-for-the-vault"></a>Povolit spravovanou identitu pro trezor

[Spravovaná identita](../active-directory/managed-identities-azure-resources/overview.md) umožňuje trezoru získat přístup k účtům úložiště. Site Recovery může vyžadovat přístup k cílovému úložišti a účtům mezipaměti nebo protokolu v závislosti na vašich požadavcích. Přístup ke spravované identitě se vyžaduje v případě, že používáte službu privátního propojení pro trezor.

1. Přejít do svého trezoru Recovery Services. V části **Nastavení**vyberte **Identita** :

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/enable-managed-identity-in-vault.png" alt-text="Snímek obrazovky zobrazující stránku nastavení identity":::

1. Změňte **stav** na **zapnuto** a vyberte **Uložit**.

   Vygeneruje se ID objektu. Trezor je nyní zaregistrován ve Azure Active Directory.

### <a name="create-private-endpoints-for-the-recovery-services-vault"></a>Vytvoření privátních koncových bodů pro Recovery Services trezor

K ochraně počítačů v místní zdrojové síti budete potřebovat jeden privátní koncový bod pro trezor v síti pro obejít. Vytvořte privátní koncový bod pomocí centra privátního spojení v Azure Portal nebo pomocí [Azure PowerShell](../private-link/create-private-endpoint-powershell.md).

1. Do vyhledávacího pole Azure Portal vyhledejte "soukromé propojení". Vyberte **privátní odkaz** pro přechod na centrum privátních odkazů:

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/search-private-links.png" alt-text="Snímek obrazovky, který ukazuje hledání Azure Portal pro centrum privátních odkazů.":::

1. V levém podokně vyberte **soukromé koncové body**. Na stránce **privátní koncové body** vyberte **Přidat** a začněte vytvářet privátní koncový bod pro svůj trezor:

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints.png" alt-text="Snímek obrazovky, který ukazuje, jak vytvořit privátní koncový bod v rámci centra privátních odkazů.":::

1. Na stránce **Vytvoření privátního koncového bodu** zadejte podrobnosti pro vytvoření připojení privátního koncového bodu.

   1. **Základy**. Zadejte základní podrobnosti o vašich soukromých koncových bodech. Použijte oblast, kterou jste použili pro obcházení sítě:

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-basic-tab.png" alt-text="Snímek obrazovky, který zobrazuje kartu Basic pro vytvoření privátního koncového bodu.":::

   1. **Prostředek**. Na této kartě je nutné zadat prostředek platformy jako služby, pro který chcete vytvořit připojení. V části **typ prostředku** pro vybrané předplatné vyberte **Microsoft. RecoveryServices/trezory**. V části **prostředek**vyberte název vašeho trezoru Recovery Services. Jako **cílový dílčí prostředek**vyberte **Azure Site Recovery** .

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-resource-tab.png" alt-text="Snímek obrazovky zobrazující kartu prostředků pro propojení s privátním koncovým bodem.":::

   1. **Konfigurace**. Na této kartě zadejte nepoužívané sítě a podsíť, kde chcete vytvořit privátní koncový bod. 

      Vyberte **Ano**, pokud chcete povolit integraci s privátní zónou DNS.
      Vyberte existující zónu DNS nebo vytvořte novou. Vyberete-li možnost **Ano** , automaticky propojí zónu se sítí obejít. Tato akce také přidá záznamy DNS, které jsou požadovány pro překlad názvů služby DNS pro nové IP adresy a plně kvalifikované názvy domén vytvořené pro soukromý koncový bod.

      Ujistěte se, že se rozhodnete vytvořit novou zónu DNS pro každý nový privátní koncový bod připojující se ke stejnému trezoru. Pokud zvolíte existující privátní zónu DNS, přepíší se předchozí záznamy CNAME. Než budete pokračovat, zobrazte si doprovodné materiály k [privátnímu koncovému bodu](../private-link/private-endpoint-overview.md#private-endpoint-properties) .

      Pokud má vaše prostředí model hvězdicového a paprskového modelu, budete pro celou instalaci potřebovat jenom jeden privátní koncový bod a jenom jednu soukromou zónu DNS. Důvodem je to, že mezi všemi vašimi virtuálními sítěmi je už povolený partnerský vztah. Další informace najdete v tématu [integrace služby DNS privátního koncového bodu](../private-link/private-endpoint-dns.md#virtual-network-workloads-without-custom-dns-server).

      Pokud chcete ručně vytvořit privátní zónu DNS, postupujte podle kroků v části [Vytvoření privátních zón DNS a ruční přidání záznamů DNS](#create-private-dns-zones-and-add-dns-records-manually).

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-configuration-tab.png" alt-text="Snímek obrazovky, který zobrazuje kartu konfigurace pro konfiguraci privátního koncového bodu.":::

   1. **Značky**. Volitelně můžete přidat značky pro privátní koncový bod.

   1. **Přečtěte si téma \+ Create**. Po dokončení ověření vyberte **vytvořit** a vytvořte privátní koncový bod.

Po vytvoření privátního koncového bodu se do privátního koncového bodu přidají pět plně kvalifikovaných názvů domény (FQDN). Tyto odkazy umožňují počítačům v místní síti přistupovat přes síť s obcházejícím a všemi potřebnými Site Recovery mikroslužby v kontextu trezoru. Můžete použít stejný soukromý koncový bod pro ochranu jakéhokoli počítače Azure v síti s obejít a všemi partnerskými sítěmi.

V tomto vzoru jsou naformátované pět názvů domén:

`{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

### <a name="approve-private-endpoints-for-site-recovery"></a>Schválení privátních koncových bodů pro Site Recovery

Pokud vytvoříte privátní koncový bod a Vy jste také vlastníkem Recovery Services trezoru, privátní koncový bod, který jste předtím vytvořili, se automaticky schválí během několika minut. V opačném případě musí vlastník trezoru před jeho použitím schválit privátní koncový bod. Pokud chcete požadované připojení privátního koncového bodu schválit nebo odmítnout, v části **Nastavení** na stránce trezoru obnovení klikněte na **připojení privátního koncového** bodu.

Než budete pokračovat, můžete přejít na prostředek privátního koncového bodu a zkontrolovat stav připojení:

:::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/vault-private-endpoint-connections.png" alt-text="Snímek obrazovky zobrazující stránku připojení privátního koncového bodu trezoru a seznam připojení":::

### <a name="optional-create-private-endpoints-for-the-cache-storage-account"></a><a name="create-private-endpoints-for-the-cache-storage-account"></a>Volitelné Vytvoření privátních koncových bodů pro účet úložiště mezipaměti

Pomocí privátního koncového bodu můžete Azure Storage. Vytváření privátních koncových bodů pro přístup do úložiště je pro Azure Site Recovery replikaci volitelné. Pokud vytvoříte privátní koncový bod pro úložiště, budete potřebovat privátní koncový bod pro účet úložiště mezipaměti nebo protokolu ve virtuální síti, která se nepoužívá.

> [!NOTE]
> Soukromé koncové body pro úložiště lze vytvořit pouze v účtech úložiště Pro obecné účely v2. Informace o cenách najdete v tématu [ceny objektů blob stránky Azure](https://azure.microsoft.com/pricing/details/storage/page-blobs/).

Pokud chcete vytvořit účet úložiště s privátním koncovým bodem, postupujte podle [pokynů pro vytvoření privátního úložiště](../private-link/create-private-endpoint-storage-portal.md#create-your-private-endpoint) . Nezapomeňte vybrat možnost **Ano** v části **integrace s privátní zónou DNS**. Vyberte existující zónu DNS nebo vytvořte novou.

### <a name="grant-required-permissions-to-the-vault"></a>Udělení požadovaných oprávnění k trezoru

V závislosti na nastavení budete možná potřebovat jeden nebo více účtů úložiště v cílové oblasti Azure. Potom udělte spravovaná oprávnění identity pro všechny účty úložiště mezipaměti nebo protokolu, které vyžaduje Site Recovery. V takovém případě je nutné vytvořit požadované účty úložiště předem.

Předtím, než povolíte replikaci virtuálních počítačů, musí mít spravovaná identita trezoru následující oprávnění role v závislosti na typu účtu úložiště.

- Účty úložiště založené na Správce prostředků (standardní typ):
  - [Přispěvatel](../role-based-access-control/built-in-roles.md#contributor)
  - [Přispěvatel dat v objektech blob služby Storage](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- Účty úložiště založené na Správce prostředků (typ Premium):
  - [Přispěvatel](../role-based-access-control/built-in-roles.md#contributor)
  - [Vlastník dat objektu BLOB služby Storage](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)
- Účty klasického úložiště:
  - [Přispěvatel klasických účtů úložiště](../role-based-access-control/built-in-roles.md#classic-storage-account-contributor)
  - [Role služby operátora klíče klasického účtu úložiště](../role-based-access-control/built-in-roles.md#classic-storage-account-key-operator-service-role)

Tento postup popisuje, jak přidat přiřazení role k účtu úložiště:

1. Přejděte na účet úložiště. V levém podokně vyberte **řízení přístupu (IAM)** .

1. V části **Přidat přiřazení role** vyberte **Přidat**:

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/storage-role-assignment.png" alt-text="Snímek obrazovky, který zobrazuje stránku řízení přístupu (IAM) pro účet úložiště.":::

1. Na stránce **Přidat přiřazení role** v seznamu **role** vyberte roli ze seznamu na začátku této části. Zadejte název trezoru a potom vyberte **Uložit**.

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/storage-role-assignment-select-role.png" alt-text="Snímek obrazovky, který ukazuje stránku přiřazení role přidání":::

Po přidání těchto oprávnění je potřeba, abyste povolili přístup k důvěryhodným službám Microsoftu. Přejděte na **brány firewall a virtuální sítě** a pro přístup k tomuto účtu úložiště v **výjimkách**vyberte možnost **dovolit důvěryhodným službám Microsoftu přístup k tomuto účtu úložiště** .

### <a name="protect-your-virtual-machines"></a>Chraňte své virtuální počítače

Po dokončení předchozích úkolů pokračujte v instalaci místní infrastruktury. Pokračujte provedením jedné z následujících úloh: 

- [Nasazení konfiguračního serveru pro VMware a fyzické počítače](./vmware-azure-deploy-configuration-server.md)
- [Nastavení prostředí Hyper-V pro replikaci](./hyper-v-azure-tutorial.md#set-up-the-source-environment)

Po dokončení instalace Povolte replikaci pro zdrojové počítače. Nevytvářejte infrastrukturu až po vytvoření privátních koncových bodů pro trezor v síti obejít.

### <a name="create-private-dns-zones-and-add-dns-records-manually"></a>Vytvoření privátních zón DNS a ruční přidání záznamů DNS

Pokud jste nevybrali možnost integrace s privátní zónou DNS při vytváření privátního koncového bodu pro trezor, postupujte podle kroků v této části.

Vytvořte jednu privátní zónu DNS, abyste povolili poskytovatele Site Recovery (pro počítače s technologií Hyper-V) nebo procesový Server (pro VMware/fyzické počítače) k překladu privátních plně kvalifikovaných názvů domény na privátní IP adresy.

1. Vytvořte privátní zónu DNS.

   1. Ve vyhledávacím poli **všechny služby** vyhledejte "privátní zóna DNS" a pak vyberte **privátní DNS zóna** ve výsledcích:

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/search-private-dns-zone.png" alt-text="Snímek obrazovky, který zobrazuje hledání privátní zóny DNS na stránce nové prostředky v Azure Portal.":::

   1. Na stránce **privátní DNS zóny** vyberte tlačítko **Přidat** a začněte vytvářet novou zónu.

   1. Na stránce **vytvořit privátní ZÓNU DNS** zadejte požadované podrobnosti. Jako název privátní zóny DNS zadejte **privatelink.siterecovery.windowsazure.com** . Můžete zvolit libovolnou skupinu prostředků a předplatné.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-dns-zone.png" alt-text="Snímek obrazovky zobrazující kartu základy stránky vytvořit zónu Privátní DNS.":::

   1. Pokračujte na kartu **Kontrola \+ Vytvoření** a zkontrolujte a vytvořte zónu DNS.

1. Propojit privátní zónu DNS s vaší virtuální sítí.

   Nyní je třeba propojit soukromou zónu DNS, kterou jste vytvořili, k vynechání.

   1. Přejdete do privátní zóny DNS, kterou jste vytvořili v předchozím kroku, a pak přejdete na **odkazy virtuální sítě** v levém podokně. Vyberte **Přidat**.

   1. Zadejte požadované podrobnosti. V seznamech **odběr** a **virtuální síť** vyberte podrobnosti, které odpovídají obmostění sítě. V ostatních polích nechejte výchozí hodnoty.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/add-virtual-network-link.png" alt-text="Snímek obrazovky, který ukazuje stránku přidat odkaz na virtuální síť.":::

1. Přidejte záznamy DNS.

   Teď, když jste vytvořili požadovanou privátní zónu DNS a soukromý koncový bod, musíte do zóny DNS přidat záznamy DNS.

   > [!NOTE]
   > Pokud používáte vlastní privátní zónu DNS, nezapomeňte udělat podobné položky, jak je popsáno v následujícím kroku.

   V tomto kroku musíte v privátním koncovém bodu v privátní zóně DNS vytvořit záznamy pro každý plně kvalifikovaný název domény.

   1. Přejdete do privátní zóny DNS a pak v levém podokně přejdete do části **Přehled** . Vyberte **sadu záznamů** a začněte přidávat záznamy.

   1. Na stránce **Přidat sadu záznamů** přidejte položku pro každý plně kvalifikovaný název domény a soukromou **IP adresu jako záznam typu.** Seznam plně kvalifikovaných názvů domény a IP adres najdete na stránce **privátní koncový bod** v části **Přehled**. Jak vidíte na následujícím snímku obrazovky, do sady záznamů v privátní zóně DNS se přidá první plně kvalifikovaný název domény z privátního koncového bodu.

      Tyto plně kvalifikované názvy domén odpovídají tomuto vzoru: `{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/add-record-set.png" alt-text="Snímek obrazovky zobrazující stránku přidat sadu záznamů":::

## <a name="next-steps"></a>Další kroky

Teď, když jste povolili privátní koncové body pro replikaci virtuálního počítače, si přečtěte tyto další články, které vám pomůžou najít další a související informace:

- [Nasazení místního konfiguračního serveru](./vmware-azure-deploy-configuration-server.md)
- [Nastavení zotavení po havárii místních virtuálních počítačů Hyper-V do Azure](./hyper-v-azure-tutorial.md)
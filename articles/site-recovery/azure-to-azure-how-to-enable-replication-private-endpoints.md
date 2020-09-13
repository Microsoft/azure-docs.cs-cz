---
title: Povolení replikace privátních koncových bodů v Azure Site Recovery
description: Tento článek popisuje, jak nakonfigurovat replikaci pro virtuální počítače s privátními koncovými body z jedné oblasti Azure do jiné pomocí Site Recovery.
author: Harsha-CS
ms.author: harshacs
ms.service: site-recovery
ms.topic: article
ms.date: 07/14/2020
ms.custom: references_regions
ms.openlocfilehash: 37784c4a294ccf296818f2afb1a8a345cb9d813e
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89658263"
---
# <a name="replicate-machines-with-private-endpoints"></a>Replikace počítačů pomocí privátních koncových bodů

Azure Site Recovery umožňuje používat privátní koncové body [privátního propojení Azure](../private-link/private-endpoint-overview.md) pro replikaci vašich počítačů do izolované virtuální sítě. Přístup privátního koncového bodu k trezoru obnovení se podporuje ve všech oblastech pro státní správu Azure Commercial &.

V tomto článku najdete pokyny k provedení následujících kroků:

- Vytvořte Azure Backup Recovery Services trezor pro ochranu vašich počítačů.
- Povolte spravovanou identitu pro trezor a udělte požadovaná oprávnění pro přístup k účtům úložiště zákazníků pro replikaci provozu ze zdroje do cílových umístění. Přístup ke spravovaným identitám pro úložiště je nezbytný při nastavení přístupu privátního propojení k trezoru.
- Vyžadování změn DNS pro privátní koncové body
- Vytváření a schvalování privátních koncových bodů pro trezor uvnitř virtuální sítě
- Vytvořte privátní koncové body pro účty úložiště. V případě potřeby můžete pro úložiště dál povolený veřejný přístup nebo přístup přes bránu firewall. Vytvoření privátního koncového bodu pro přístup k úložišti není pro Azure Site Recovery povinné.
  
Níže je referenční architektura, jak se mění pracovní postup replikace pomocí privátních koncových bodů.

:::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/architecture.png" alt-text="Referenční architektura pro Site Recovery s privátními koncovými body.":::

## <a name="prerequisites-and-caveats"></a>Požadavky a upozornění

- Soukromé koncové body lze vytvořit pouze pro nové trezory Recovery Services, které nemají registrovány žádné položky do trezoru. V takovém případě **je nutné před přidáním položek do trezoru vytvořit**privátní koncové body. Přečtěte si cenovou strukturu [privátních koncových bodů](https://azure.microsoft.com/pricing/details/private-link/).
- Když se pro trezor vytvoří privátní koncový bod, trezor se zamkne a **nebude přístupný ze sítí, které jsou v privátních koncových bodech**.
- Azure Active Directory v současné době nepodporuje privátní koncové body. V takovém případě musí být IP adresy a plně kvalifikované názvy domény vyžadované pro Azure Active Directory pro práci v oblasti potřeba povolit odchozí přístup ze zabezpečené sítě. Pomocí značky skupiny zabezpečení sítě Azure Active Directory a značek Azure Firewall můžete také povolit přístup k Azure Active Directory, jak je to možné.
- V podsítích zdrojového počítače i počítačů pro obnovení **se vyžadují aspoň sedm IP adres** . Když vytvoříte privátní koncový bod pro trezor, Site Recovery vytvoří pět privátních odkazů pro přístup ke svým mikroslužbám. Pokud povolíte replikaci, přidá další dva privátní odkazy pro párování zdrojové a cílové oblasti.
- Ve zdrojové i podsítě pro obnovení **je vyžadována jedna další IP adresa** . Tato IP adresa je potřeba jenom v případě, že potřebujete používat privátní koncové body připojující se k účtům úložiště mezipaměti.
  Soukromé koncové body pro úložiště lze vytvořit pouze u Pro obecné účelyho typu v2. Přečtěte si cenovou strukturu pro [přenos dat v GPv2](https://azure.microsoft.com/pricing/details/storage/page-blobs/).

 ## <a name="creating-and-using-private-endpoints-for-site-recovery"></a>Vytváření a používání privátních koncových bodů pro Site Recovery

 Tato část pojednává o krocích při vytváření a používání privátních koncových bodů pro Azure Site Recovery v rámci virtuálních sítí.

> [!NOTE]
> Důrazně doporučujeme, abyste provedete tyto kroky ve stejné sekvenci, jak je uvedeno níže. V takovém případě může dojít k tomu, že se vykreslí trezor, který nedokáže použít privátní koncové body a vyžaduje, abyste tento proces restartovali v novém trezoru.

## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

Trezor služby Recovery Services je entita, která obsahuje informace o replikaci počítačů a slouží ke spuštění operací Site Recovery. Další informace najdete v tématu [Vytvoření trezoru Recovery Services](./azure-to-azure-tutorial-enable-replication.md#create-a-recovery-services-vault).

## <a name="enable-the-managed-identity-for-the-vault"></a>Povolte spravovanou identitu pro trezor.

[Spravovaná identita](../active-directory/managed-identities-azure-resources/overview.md) umožňuje trezoru získat přístup k účtům úložiště zákazníka. Site Recovery musí mít přístup ke zdrojovému úložišti, cílovému úložišti a účtům úložiště cache a log v závislosti na požadavcích na scénář.
Přístup spravovaných identit je nezbytný, když používáte pro trezor službu privátních odkazů.

1. Přejít do svého trezoru Recovery Services. V části _Nastavení_vyberte **Identita** .

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/enable-managed-identity-in-vault.png" alt-text="Zobrazuje Azure Portal a stránku Recovery Services.":::

1. Změňte **stav** na _zapnuto_ a vyberte **Uložit**.

1. Vygeneruje se **ID objektu** , které označuje, že je trezor teď zaregistrovaný ve Azure Active Directory.

## <a name="create-private-endpoints-for-the-recovery-services-vault"></a>Vytvoření privátních koncových bodů pro Recovery Services trezor

Pokud chcete povolit převzetí služeb při selhání i navrácení služeb po obnovení pro virtuální počítače Azure, budete potřebovat dva privátní koncové body pro trezor. Jeden privátní koncový bod pro ochranu počítačů ve zdrojové síti a další pro opětovné zabezpečení převzetí služeb při selhání u počítačů v síti pro obnovení.

Ujistěte se, že jste v cílové oblasti mohli vytvořit virtuální síť pro obnovení i v průběhu tohoto procesu instalace.

Vytvořte první privátní koncový bod vašeho trezoru v rámci zdrojové virtuální sítě pomocí centra privátního spojení na portálu nebo prostřednictvím [Azure PowerShell](../private-link/create-private-endpoint-powershell.md). Vytvořte druhý soukromý koncový bod pro trezor v rámci sítě pro obnovení. V této části je postup vytvoření privátního koncového bodu ve zdrojové síti. Opakujte stejné pokyny k vytvoření druhého privátního koncového bodu.

1. Na panelu hledání Azure Portal vyhledejte a vyberte "soukromé propojení". Tato akce přejde do centra privátních odkazů.

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/search-private-links.png" alt-text="Zobrazuje hledání Azure Portal pro centrum privátních odkazů.":::

1. V levém navigačním panelu vyberte **soukromé koncové body**. Po vytvoření privátního koncového bodu pro svůj trezor vyberte na stránce privátní koncové body možnost ** \+ Přidat** .

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-endpoints.png" alt-text="Ukazuje vytvoření privátního koncového bodu v centru privátních odkazů.":::

1. Jednou v prostředí pro vytvoření privátního koncového bodu je nutné zadat podrobnosti pro vytvoření připojení privátního koncového bodu.

   1. **Základy**: Vyplňte základní podrobnosti vašich privátních koncových bodů. Oblast by měla být stejná jako u zdrojových počítačů.

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-endpoints-basic-tab.png" alt-text="Zobrazuje kartu Basic, podrobnosti projektu, předplatné a další související pole pro vytvoření privátního koncového bodu v Azure Portal.":::

   1. **Prostředek**: Tato karta vyžaduje, abyste uváděli prostředek platformy jako služby, pro který chcete vytvořit připojení. Pro vybrané předplatné vyberte _Microsoft. RecoveryServices/trezory_ z **typu prostředku** . Pak zvolte název trezoru Recovery Services pro **prostředek** a nastavte _Azure Site Recovery_ jako **cílový dílčí prostředek**.

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-endpoints-resource-tab.png" alt-text="Zobrazuje kartu prostředků, typ prostředku, prostředek a cíl pro propojení s privátním koncovým bodem v Azure Portal.":::

   1. **Konfigurace**: v části Konfigurace zadejte virtuální síť a podsíť, ve které chcete vytvořit privátní koncový bod. Tato virtuální síť je síť, ve které se virtuální počítač nachází. Vyberte **Ano**, pokud chcete povolit integraci s privátní zónou DNS. Vyberte už vytvořenou zónu DNS nebo vytvořte novou. Vyberete-li možnost **Ano** , automaticky propojí zónu se zdrojovou virtuální sítí a přidá záznamy DNS, které jsou požadovány pro překlad DNS nových IP adres a plně kvalifikované názvy domén vytvořené pro soukromý koncový bod.

      Ujistěte se, že se rozhodnete vytvořit novou zónu DNS pro každý nový privátní koncový bod připojující se ke stejnému trezoru. Pokud zvolíte existující privátní zónu DNS, přepíší se předchozí záznamy CNAME. Než budete pokračovat, přečtěte si [pokyny k privátnímu koncovému bodu](../private-link/private-endpoint-overview.md#private-endpoint-properties) .

      Pokud má vaše prostředí model hvězdicového a paprskového modelu, budete pro celou instalaci potřebovat jenom jeden privátní koncový bod a jenom jednu soukromou zónu DNS, protože mezi nimi už jsou povolené partnerské vztahy mezi všemi vašimi virtuálními sítěmi. Další informace najdete v tématu [integrace služby DNS privátního koncového bodu](../private-link/private-endpoint-dns.md#virtual-network-workloads-without-custom-dns-server).

      Pokud chcete ručně vytvořit privátní zónu DNS, postupujte podle kroků v části [Vytvoření privátních zón DNS a ruční přidání záznamů DNS](#create-private-dns-zones-and-add-dns-records-manually).

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-endpoints-configuration-tab.png" alt-text="Zobrazuje kartu konfigurace s poli integrace sítě a DNS pro konfiguraci privátního koncového bodu v Azure Portal.":::

   1. **Značky**: Volitelně můžete přidat značky pro privátní koncový bod.

   1. **Revize \+ vytvořit**: po dokončení ověření vyberte **vytvořit** a vytvořte privátní koncový bod.

Po vytvoření privátního koncového bodu se do privátního koncového bodu přidají pět plně kvalifikovaných názvů domén. Tyto odkazy umožňují počítačům ve virtuální síti získat přístup ke všem požadovaným Site Recovery mikroslužbám v kontextu trezoru. Když později povolíte replikaci, přidají se do stejného privátního koncového bodu dva další plně kvalifikované názvy domény.

Pět názvů domén je formátováno následujícím vzorem:

`{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

## <a name="approve-private-endpoints-for-site-recovery"></a>Schválení privátních koncových bodů pro Site Recovery

Pokud uživatel vytvářející soukromý koncový bod je zároveň vlastníkem Recovery Services trezoru, pak je privátní koncový bod vytvořený výše automaticky schválen během několika minut. V opačném případě musí vlastník trezoru před jeho použitím schválit privátní koncový bod. Pokud chcete požadované připojení privátního koncového bodu schválit nebo odmítnout, v části nastavení na stránce trezoru pro obnovení použijte **připojení privátního koncového** bodu.

Než budete pokračovat, můžete přejít na prostředek privátního koncového bodu a zkontrolovat stav připojení.

:::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/vault-private-endpoint-connections.png" alt-text="Zobrazuje stránku připojení privátního koncového bodu trezoru a seznam připojení v Azure Portal.":::

## <a name="optional-create-private-endpoints-for-the-cache-storage-account"></a><a name="create-private-endpoints-for-the-cache-storage-account"></a>Volitelné Vytvoření privátních koncových bodů pro účet úložiště mezipaměti

Může se použít privátní koncový bod pro Azure Storage. Vytváření privátních koncových bodů pro přístup do úložiště je pro Azure Site Recovery replikaci _volitelné_ . Při vytváření privátního koncového bodu pro úložiště platí následující požadavky:

- Pro účet úložiště mezipaměti nebo protokolu v zdrojové virtuální síti potřebujete privátní koncový bod.
- V době opětovné ochrany počítačů s podporou převzetí služeb při selhání v síti pro obnovení potřebujete druhý soukromý koncový bod. Tento soukromý koncový bod je pro nový účet úložiště vytvořený v cílové oblasti.

> [!NOTE]
> Privátní koncový bod pro úložiště se dá vytvořit jenom v účtech úložiště **pro obecné účely v2** . Informace o cenách najdete v tématu [ceny objektů blob stránky úrovně Standard](https://azure.microsoft.com/pricing/details/storage/page-blobs/).

Pokud chcete vytvořit účet úložiště s privátním koncovým bodem, postupujte podle [pokynů pro vytvoření privátního úložiště](../private-link/create-private-endpoint-storage-portal.md#create-your-private-endpoint) . Ujistěte se, že pro integraci s privátní zónou DNS vyberte **Ano** . Vyberte již vytvořenou zónu DNS nebo vytvořte novou.

## <a name="grant-required-permissions-to-the-vault"></a>Udělení požadovaných oprávnění k trezoru

Pokud vaše virtuální počítače používají spravované disky, musíte oprávnění spravované identity udělit jenom účtům úložiště mezipaměti. V případě, že virtuální počítače používají nespravované disky, musíte udělit spravovaná oprávnění identity pro zdroj, mezipaměť a cílové účty úložiště. V takovém případě je potřeba vytvořit cílový účet úložiště předem.

Před povolením replikace virtuálních počítačů musí mít spravovaná identita trezoru následující oprávnění role v závislosti na typu účtu úložiště:

- Účty úložiště založené na Správce prostředků (standardní typ):
  - [Přispěvatel](../role-based-access-control/built-in-roles.md#contributor)
  - [Přispěvatel dat v objektech blob služby Storage](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- Účty úložiště založené na Správce prostředků (typ Premium):
  - [Přispěvatel](../role-based-access-control/built-in-roles.md#contributor)
  - [Vlastník dat objektu BLOB služby Storage](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)
- Účty klasického úložiště:
  - [Přispěvatel klasických účtů úložiště](../role-based-access-control/built-in-roles.md#classic-storage-account-contributor)
  - [Role služby operátora klíče klasického účtu úložiště](../role-based-access-control/built-in-roles.md#classic-storage-account-key-operator-service-role)

Následující postup popisuje, jak přidat přiřazení role k účtům úložiště, a to v jednom okamžiku:

1. Přejděte na účet úložiště a přejděte na stránku **řízení přístupu (IAM)** na levé straně stránky.

1. Po **řízení přístupu (IAM)** v poli přidat přiřazení role vyberte **Přidat**.

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/storage-role-assignment.png" alt-text="Zobrazuje stránku řízení přístupu (IAM) na účtu úložiště a tlačítko Přidat přiřazení role v Azure Portal.":::

1. Na straně přidat přiřazení role vyberte roli ze seznamu výše v rozevíracím seznamu **role** . Zadejte **název** trezoru a vyberte **Uložit**.

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/storage-role-assignment-select-role.png" alt-text="Zobrazuje stránku řízení přístupu (IAM) v účtu úložiště a možnosti pro výběr role a objektu zabezpečení, který této roli udělí v Azure Portal.":::

Kromě těchto oprávnění musí být důvěryhodné služby společnosti Microsoft také povolený přístup. Přejděte do části brány firewall a virtuální sítě a zaškrtněte políčko pro přístup k tomuto účtu úložiště důvěřovat důvěryhodným službám **Microsoftu.**

## <a name="protect-your-virtual-machines"></a>Chraňte své virtuální počítače

Po dokončení všech výše uvedených konfigurací pokračujte tím, že zapnete replikaci pro virtuální počítače. Všechny operace Site Recovery fungují bez dalších kroků, pokud se při vytváření privátních koncových bodů v trezoru použila integrace DNS. Pokud se ale zóny DNS vytvoří a nakonfigurují ručně, budete po povolení replikace potřebovat další kroky k přidání konkrétních záznamů DNS ve zdrojové i cílové zóně DNS. Podrobnosti a postupy najdete v tématech [Vytvoření privátních zón DNS a ruční přidání záznamů DNS](#create-private-dns-zones-and-add-dns-records-manually).

## <a name="create-private-dns-zones-and-add-dns-records-manually"></a>Vytvoření privátních zón DNS a ruční přidání záznamů DNS

Pokud jste nevybrali možnost integrace s privátní zónou DNS v době vytváření privátního koncového bodu pro trezor, postupujte podle kroků v této části.

Vytvořte jednu privátní zónu DNS, abyste mohli agent mobility přeložit na privátní IP adresy plně kvalifikované názvy domén.

1. Vytvoření privátní zóny DNS

   1. Vyhledejte "Privátní DNS Zone" na panelu hledání **všechny služby** a v rozevíracím seznamu vyberte možnost privátní DNS zóny.

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/search-private-dns-zone.png" alt-text="Na stránce nové prostředky v Azure Portal ukazuje hledání zóny privátní DNS.":::

   1. Jednou na stránce Privátní DNS zóny vyberte tlačítko ** \+ Přidat** a začněte vytvářet novou zónu.

   1. Na stránce Vytvoření privátní zóny DNS vyplňte požadované podrobnosti. Jako název zadejte název privátní zóny DNS `privatelink.siterecovery.windowsazure.com` . Pro vytvoření můžete vybrat libovolnou skupinu prostředků a všechny její odběry.

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-dns-zone.png" alt-text="Zobrazí kartu základy stránky vytvořit Privátní DNS zónu a podrobnosti o souvisejícím projektu v Azure Portal.":::

   1. Pokračujte na kartu **Kontrola \+ Vytvoření** a zkontrolujte a vytvořte zónu DNS.

1. Propojení privátní zóny DNS s vaší virtuální sítí

   Privátní zóny DNS vytvořené výše se teď musí propojit s virtuální sítí, kde jsou vaše servery aktuálně. Také je potřeba propojit privátní zónu DNS s cílovou virtuální sítí předem.

   1. Přejděte do privátní zóny DNS, kterou jste vytvořili v předchozím kroku, a přejděte na **odkazy virtuální sítě** na levé straně stránky. Po výběru vyberte tlačítko ** \+ Přidat** .

   1. Vyplňte požadované podrobnosti. Pole **odběr** a **virtuální síť** musí být vyplněna odpovídajícími podrobnostmi o virtuální síti, ve které vaše servery existují. Ostatní pole musí být ponechána tak, jak je.

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/add-virtual-network-link.png" alt-text="Zobrazuje stránku pro přidání odkazu virtuální sítě s názvem odkazu, odběrem a související virtuální sítí v Azure Portal.":::

1. Přidat záznamy DNS

   Po vytvoření požadovaných privátních zón DNS a privátních koncových bodů budete muset přidat záznamy DNS do zón DNS.

   > [!NOTE]
   > Pokud používáte vlastní privátní zónu DNS, zajistěte, aby byly provedeny podobné položky, jak je popsáno níže.

   Tento krok vyžaduje, abyste v privátním koncovém bodu v privátní zóně DNS provedli záznamy pro každý plně kvalifikovaný název domény.

   1. Přejděte do privátní zóny DNS a přejděte do části **Přehled** na levé straně stránky. Potom vyberte ** \+ sadu záznamů** a začněte přidávat záznamy.

   1. Na stránce Přidat sadu záznamů, která se otevře, přidejte položku pro každý plně kvalifikovaný název domény a soukromou _IP adresu jako záznam typu._ Seznam plně kvalifikovaných názvů domén a IP adres se dá získat ze stránky privátního koncového bodu v **přehledu**. Jak je znázorněno v následujícím příkladu, do sady záznamů v privátní zóně DNS se přidá první plně kvalifikovaný název domény z privátního koncového bodu.

      Vzor těchto plně kvalifikovaných názvů domén odpovídá vzoru: `{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/add-record-set.png" alt-text="Zobrazí stránku, kde můžete přidat záznam typu DNS pro plně kvalifikovaný název domény do privátního koncového bodu v Azure Portal.":::

   > [!NOTE]
   > Po povolení replikace se v obou oblastech vytvoří v privátních koncových bodech dva další plně kvalifikované názvy domén. Ujistěte se také, že jste přidali záznamy DNS pro tyto nově vytvořené názvy domén.

## <a name="next-steps"></a>Další kroky

Teď, když jste povolili privátní koncové body pro replikaci virtuálního počítače, se na těchto dalších stránkách zobrazí další a související informace:

- [Replikace virtuálních počítačů Azure do jiné oblasti Azure](./azure-to-azure-how-to-enable-replication.md)
- [Kurz: nastavení zotavení po havárii pro virtuální počítače Azure](./azure-to-azure-tutorial-enable-replication.md)
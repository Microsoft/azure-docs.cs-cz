---
title: Privátní koncové body
description: Pochopení procesu vytváření privátních koncových bodů pro Azure Backup a scénářů, kdy použití privátních koncových bodů pomáhá udržet zabezpečení vašich prostředků.
ms.topic: conceptual
ms.date: 05/07/2020
ms.openlocfilehash: 7423157abbc0833394af055f5e31f724caa10b46
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103224703"
---
# <a name="private-endpoints-for-azure-backup"></a>Soukromé koncové body pro Azure Backup

Azure Backup umožňuje bezpečně zálohovat a obnovovat data z trezorů Recovery Services pomocí [privátních koncových bodů](../private-link/private-endpoint-overview.md). Soukromé koncové body používají jednu nebo více privátních IP adres z vaší virtuální sítě a efektivně donášejí službu do vaší virtuální sítě.

Tento článek vám pomůže pochopit proces vytváření privátních koncových bodů pro Azure Backup a scénáře, kdy použití privátních koncových bodů pomáhá udržet zabezpečení vašich prostředků.

## <a name="before-you-start"></a>Než začnete

- Soukromé koncové body lze vytvořit pouze pro nové trezory Recovery Services (které nemají zaregistrovány žádné položky do trezoru). Proto je nutné před pokusem o ochranu položek do trezoru vytvořit privátní koncové body.
- Jedna virtuální síť může obsahovat privátní koncové body pro několik trezorů Recovery Services. Kromě toho může mít jeden Recovery Services trezor pro něj privátní koncové body ve více virtuálních sítích. Maximální počet privátních koncových bodů, které je možné vytvořit pro trezor, je však 12.
- Po vytvoření privátního koncového bodu pro trezor se trezor zamkne. Nebude k dispozici (pro zálohování a obnovení) z sítí kromě těch, které obsahují soukromý koncový bod pro trezor. Pokud jsou všechny privátní koncové body trezoru odebrány, bude trezor přístupný ze všech sítí.
- Připojení privátního koncového bodu pro zálohování používá celkem 11 privátních IP adres ve vaší podsíti, včetně těch, které používá Azure Backup pro úložiště. Toto číslo může být pro určité oblasti Azure vyšší (až 25). Proto doporučujeme, abyste při pokusu o vytvoření privátních koncových bodů pro zálohování měli k dispozici dostatek privátních IP adres.
- I když je Recovery Services trezor používá (obojí) Azure Backup a Azure Site Recovery, Tento článek popisuje použití privátních koncových bodů jenom pro Azure Backup.
- Azure Active Directory v současné době nepodporuje privátní koncové body. Takže IP adresy a plně kvalifikované názvy domény, které Azure Active Directory pro práci v oblasti, musí mít povolený odchozí přístup ze zabezpečené sítě při provádění zálohování databází ve virtuálních počítačích Azure a při zálohování pomocí agenta MARS. Můžete také použít značky NSG a značky Azure Firewall pro povolení přístupu ke službě Azure AD (podle potřeby).
- U privátních koncových bodů se virtuální sítě se zásadami sítě nepodporují. Než budete pokračovat, budete muset [zakázat zásady sítě](https://docs.microsoft.com/azure/private-link/disable-private-endpoint-network-policy) .
- Pokud jste ho zaregistrovali před 1 2020, je nutné znovu zaregistrovat poskytovatele prostředků Recovery Services k předplatnému. Pokud chcete poskytovatele znovu zaregistrovat, přejděte do svého předplatného v Azure Portal, v levém navigačním panelu přejděte na **poskytovatel prostředků** , vyberte **Microsoft. RecoveryServices** a vyberte **znovu registrovat**.
- [Obnovení mezi oblastmi](backup-create-rs-vault.md#set-cross-region-restore) pro SQL a SAP HANA zálohy databáze není podporované, pokud má Trezor povolené privátní koncové body.
- Když přesunete Recovery Services trezor již s použitím privátních koncových bodů na nového tenanta, budete muset aktualizovat trezor Recovery Services a znovu vytvořit a znovu nakonfigurovat spravovanou identitu trezoru a vytvořit nové privátní koncové body podle potřeby (které by měly být v novém tenantovi). Pokud to neuděláte, operace zálohování a obnovení začnou selhat. Všechna oprávnění řízení přístupu na základě role (RBAC) nastavená v rámci předplatného se taky musí překonfigurovat.

## <a name="recommended-and-supported-scenarios"></a>Doporučené a podporované scénáře

I když jsou v trezoru povolené privátní koncové body, používají se k zálohování a obnovení úloh SQL a SAP HANA jenom na virtuálních počítačích Azure a na zálohování agenta MARS. Trezor můžete použít i pro zálohování dalších úloh (nevyžadují i privátní koncové body). Kromě zálohování úloh SQL a SAP HANA a zálohování pomocí agenta MARS se také k provádění obnovení souborů pro zálohování virtuálních počítačů Azure používá privátní koncové body. Další informace najdete v následující tabulce:

| Zálohování úloh na virtuálním počítači Azure (SQL, SAP HANA), zálohování pomocí agenta MARS | Použití privátních koncových bodů se doporučuje k povolení zálohování a obnovení, aniž byste museli přidávat do seznamu povolených IP adres nebo plně kvalifikovaných názvů domén pro Azure Backup nebo Azure Storage z vašich virtuálních sítí. V takovém případě zajistěte, aby virtuální počítače, které hostují databáze SQL, mohly získat IP adresy nebo plně kvalifikované názvy domény služby Azure |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Zálohování virtuálních počítačů Azure**                                         | Zálohování virtuálního počítače nevyžaduje povolení přístupu k žádným IP adresám nebo plně kvalifikovaným názvům domén. Proto nevyžaduje privátní koncové body pro zálohování a obnovení disků.  <br><br>   Obnovení souborů z trezoru obsahujícího privátní koncové body by ale mělo být omezené na virtuální sítě, které obsahují privátní koncový bod pro trezor. <br><br>    Pokud používáte nespravované disky ACL'ed, ujistěte se, že účet úložiště, který obsahuje disky, umožňuje přístup k **důvěryhodným službám Microsoftu** , pokud je ACL'ed. |
| **Zálohování souborů Azure**                                      | Zálohy souborů Azure se ukládají v místním účtu úložiště. Proto nevyžaduje privátní koncové body pro zálohování a obnovení. |

## <a name="get-started-with-creating-private-endpoints-for-backup"></a>Začínáme s vytvářením privátních koncových bodů pro zálohování

Následující části popisují kroky týkající se vytváření a používání privátních koncových bodů pro Azure Backup v rámci virtuálních sítí.

>[!IMPORTANT]
> Důrazně doporučujeme postupovat podle kroků ve stejné posloupnosti, jak je uvedeno v tomto dokumentu. V takovém případě může dojít k tomu, že se vygenerování trezoru nekompatibilním s používáním privátních koncových bodů a vyžaduje restartování procesu v novém trezoru.

## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

Privátní koncové body pro zálohování je možné vytvořit jenom pro Recovery Services trezorů, které nemají chráněné žádné položky (nebo se v minulosti neudělaly žádné položky, které se do ní nepokusily chránit nebo zaregistrovat). Proto doporučujeme vytvořit nový trezor pro zahájení. Další informace o vytvoření nového trezoru najdete v tématu  [Vytvoření a konfigurace trezoru Recovery Services](backup-create-rs-vault.md).

V [této části](#create-a-recovery-services-vault-using-the-azure-resource-manager-client) se dozvíte, jak vytvořit trezor pomocí klienta Azure Resource Manager. Tím se vytvoří trezor s spravovanou identitou, který už je povolený.

## <a name="enable-managed-identity-for-your-vault"></a>Povolit spravovanou identitu pro svůj trezor

Spravované identity umožňují trezoru vytvářet a používat soukromé koncové body. Tato část pojednává o povolení spravované identity vašeho trezoru.

1. Přejít na Recovery Services trezor – > **identitu**.

    ![Změnit stav identity na zapnuto](./media/private-endpoints/identity-status-on.png)

1. Změňte **stav** na **zapnuto** a vyberte **Uložit**.

1. Generuje se **ID objektu** , což je spravovaná identita trezoru.

    >[!NOTE]
    >Po povolení nesmí být spravovaná identita zakázaná **(ještě dočasně** ). Zakázání spravované identity může vést k nekonzistentnímu chování.

## <a name="grant-permissions-to-the-vault-to-create-required-private-endpoints"></a>Udělení oprávnění k trezoru pro vytvoření požadovaných privátních koncových bodů

Pro vytvoření požadovaných privátních koncových bodů pro Azure Backup musí mít trezor (spravovaná identita trezoru) oprávnění pro následující skupiny prostředků:

- Skupina prostředků, která obsahuje cílovou virtuální síť
- Skupina prostředků, ve které se mají vytvořit privátní koncové body
- Skupina prostředků, která obsahuje zóny Privátní DNS, jak je popsáno [v podrobnostech](#create-private-endpoints-for-azure-backup)

Doporučujeme, abyste roli **přispěvatele** nadělili pro tyto tři skupiny prostředků do trezoru (spravovaná identita). Následující postup popisuje, jak to provést pro konkrétní skupinu prostředků (je potřeba provést u každé ze tří skupin prostředků):

1. Přejděte do skupiny prostředků a na levém panelu přejděte na **Access Control (IAM)** .
1. V **Access Control** klikněte na **Přidat přiřazení role**.

    ![Přidat přiřazení role](./media/private-endpoints/add-role-assignment.png)

1. V podokně **Přidat přiřazení role** vyberte jako **roli** možnost **Přispěvatel** a jako **objekt zabezpečení** použijte **název** trezoru. Vyberte svůj trezor a po dokončení vyberte **Uložit** .

    ![Výběr role a objektu zabezpečení](./media/private-endpoints/choose-role-and-principal.png)

Pokud chcete spravovat oprávnění na podrobnější úrovni, přečtěte si téma [Vytvoření rolí a oprávnění ručně](#create-roles-and-permissions-manually).

## <a name="create-private-endpoints-for-azure-backup"></a>Vytvoření privátních koncových bodů pro Azure Backup

V této části se dozvíte, jak vytvořit privátní koncový bod pro svůj trezor.

1. Přejděte do trezoru, který jste vytvořili výše, a v levém navigačním panelu přejděte na **připojení privátního koncového bodu** . V horní části vyberte **+ privátní koncový bod** a začněte vytvářet nový privátní koncový bod pro tento trezor.

    ![Vytvořit nový privátní koncový bod](./media/private-endpoints/new-private-endpoint.png)

1. Jednou v procesu **Vytvoření privátního koncového bodu** budete muset zadat podrobnosti pro vytvoření připojení privátního koncového bodu.
  
    1. **Základy**: Vyplňte základní podrobnosti vašich privátních koncových bodů. Oblast by měla být stejná jako trezor a prostředek, který se zálohuje.

        ![Vyplnit základní podrobnosti](./media/private-endpoints/basics-tab.png)

    1. **Prostředek**: Tato karta vyžaduje, abyste vybrali prostředek PaaS, pro který chcete vytvořit připojení. Pro požadované předplatné vyberte **Microsoft. RecoveryServices/trezory** z typu prostředku. Po dokončení vyberte název vašeho trezoru Recovery Services jako **prostředek** a **AzureBackup** jako **cílový dílčí prostředek**.

        ![Vyberte prostředek pro vaše připojení.](./media/private-endpoints/resource-tab.png)

    1. **Konfigurace**: v části Konfigurace zadejte virtuální síť a podsíť, ve které chcete vytvořit privátní koncový bod. Toto bude virtuální síť, ve které se virtuální počítač nachází.

        Chcete-li se připojit soukromě, potřebujete požadované záznamy DNS. V závislosti na nastavení sítě můžete vybrat jednu z následujících možností:

          - Integrace privátního koncového bodu s privátní zónou DNS: Pokud chcete integrovat, vyberte **Ano** .
          - Použijte vlastní server DNS: Pokud chcete použít vlastní server DNS, zvolte **ne** .

        Správa záznamů DNS pro obě jsou [popsány později](#manage-dns-records).

          ![Zadejte virtuální síť a podsíť.](./media/private-endpoints/configuration-tab.png)

    1. Volitelně můžete přidat **značky** pro privátní koncový bod.
    1. Až se dokončí zadání podrobností, pokračujte v **kontrole a vytváření** . Po dokončení ověření vyberte **vytvořit** a vytvořte tak privátní koncový bod.

## <a name="approve-private-endpoints"></a>Schválení privátních koncových bodů

Pokud uživatel vytvářející soukromý koncový bod je zároveň vlastníkem Recovery Services trezoru, pak se privátní koncový bod, který jste vytvořili výše, automaticky schválí. V opačném případě musí vlastník trezoru před jeho použitím schválit privátní koncový bod. Tato část popisuje ruční schválení privátních koncových bodů prostřednictvím Azure Portal.

V tématu [ruční schválení privátních koncových bodů pomocí klienta Azure Resource Manager](#manual-approval-of-private-endpoints-using-the-azure-resource-manager-client) použijte klienta Azure Resource Manager pro schvalování privátních koncových bodů.

1. V úložišti Recovery Services přejděte na levý panel na **připojení privátního koncového bodu** .
1. Vyberte připojení privátního koncového bodu, které chcete schválit.
1. V horním panelu vyberte **schválit** . Můžete také vybrat možnost **odmítnout** nebo **Odebrat** , pokud chcete odmítat nebo odstranit připojení koncového bodu.

    ![Schválení privátních koncových bodů](./media/private-endpoints/approve-private-endpoints.png)

## <a name="manage-dns-records"></a>Správa záznamů DNS

Jak už bylo popsáno, budete potřebovat záznamy DNS ve svých privátních zónách DNS nebo serverech, abyste se mohli připojit soukromě. Svůj privátní koncový bod můžete integrovat přímo s privátními zónami DNS Azure, případně můžete k tomu využít vlastní servery DNS, které jsou založené na vašich preferencích sítě. To se bude muset udělat pro všechny tři služby: zálohování, objekty BLOB a fronty.

### <a name="when-integrating-private-endpoints-with-azure-private-dns-zones"></a>Při integraci privátních koncových bodů s privátními zónami DNS Azure

Pokud se rozhodnete integrovat privátní koncový bod s privátními zónami DNS, bude služba Backup přidávat požadované záznamy DNS. Privátní zóny DNS, které se používají, můžete zobrazit v části **Konfigurace DNS** privátního koncového bodu. Pokud tyto zóny DNS nejsou k dispozici, budou vytvořeny automaticky při vytváření privátního koncového bodu. Je však nutné ověřit, zda je vaše virtuální síť (obsahující prostředky, které mají být zálohovány) správně propojena se všemi třemi privátními zónami DNS, jak je popsáno níže.

![Konfigurace DNS v privátní zóně DNS Azure](./media/private-endpoints/dns-configuration.png)

#### <a name="validate-virtual-network-links-in-private-dns-zones"></a>Ověřit odkazy virtuální sítě v privátních zónách DNS

Pro každou z výše uvedených **privátních zón DNS** (pro zálohování, objekty BLOB a fronty) udělejte toto:

1. V levém navigačním panelu přejděte na příslušnou možnost **odkazy virtuální sítě** .
1. Měli byste být schopni zobrazit položku pro virtuální síť, pro kterou jste vytvořili privátní koncový bod, jako je ten, který vidíte níže:

    ![Virtuální síť pro soukromý koncový bod](./media/private-endpoints/virtual-network-links.png)

1. Pokud nevidíte položku, přidejte odkaz virtuální sítě do všech zón DNS, které je nemají.

    ![Přidat odkaz virtuální sítě](./media/private-endpoints/add-virtual-network-link.png)

### <a name="when-using-custom-dns-server-or-host-files"></a>Při použití vlastního serveru DNS nebo hostitelských souborů

Pokud používáte vlastní servery DNS, budete muset vytvořit požadované zóny DNS a přidat záznamy DNS vyžadované privátními koncovými body do vašich serverů DNS. U objektů BLOB a front můžete také použít podmíněné služby pro dodávání.

#### <a name="for-the-backup-service"></a>Pro službu zálohování

1. Na serveru DNS vytvořte zónu DNS pro zálohování podle následujících zásad vytváření názvů:

    |Zóna |Služba |
    |---------|---------|
    |`privatelink.<geo>.backup.windowsazure.com`   |  Backup        |

    >[!NOTE]
    > Ve výše uvedeném textu `<geo>` odkazuje na kód oblasti (například *EUS* a *ne* pro východní USA a Severní Evropa). Kódy oblastí najdete v následujících seznamech:
    >
    > - [Všechny veřejné cloudy](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/AzureRegionCodesList.docx)
    > - [Čína](/azure/china/resources-developer-guide#check-endpoints-in-azure)
    > - [Německo](../germany/germany-developer-guide.md#endpoint-mapping)
    > - [US Gov](../azure-government/documentation-government-developer-guide.md)

1. Dál je potřeba přidat požadované záznamy DNS. Chcete-li zobrazit záznamy, které je třeba přidat do zóny DNS zálohy, přejděte do privátního koncového bodu, který jste vytvořili výše, a v levém navigačním panelu přejděte na možnost **Konfigurace služby DNS** .

    ![Konfigurace DNS pro vlastní server DNS](./media/private-endpoints/custom-dns-configuration.png)

1. Přidejte jednu položku pro každý plně kvalifikovaný název domény a IP adresu zobrazenou jako záznamy typu v zóně DNS pro zálohování. Pokud k překladu názvů používáte hostitelský soubor, zajistěte odpovídající položky v souboru hostitele pro každou IP adresu a plně kvalifikovaný název domény v závislosti na následujícím formátu:

    `<private ip><space><backup service privatelink FQDN>`

>[!NOTE]
>Jak je znázorněno na snímku obrazovky výše, zobrazují se plně kvalifikované názvy domény `xxxxxxxx.<geo>.backup.windowsazure.com` a ne `xxxxxxxx.privatelink.<geo>.backup. windowsazure.com` . V takových případech nezapomeňte zahrnout (a pokud je to potřeba, přidat) `.privatelink.` podle uvedeného formátu.

#### <a name="for-blob-and-queue-services"></a>Pro objekty BLOB a Queue Services

U objektů BLOB a front můžete na serveru DNS použít podmíněné servery pro směrování nebo vytvořit zóny DNS.

##### <a name="if-using-conditional-forwarders"></a>Pokud používáte podmíněné předávací aplikace

Pokud používáte podmíněné služby pro dodávání, přidejte služby pro dopředné názvy objektů BLOB a fronty následujícím způsobem:

|FQDN  |IP adresa  |
|---------|---------|
|`privatelink.blob.core.windows.net`     |  168.63.129.16       |
|`privatelink.queue.core.windows.net`     | 168.63.129.16        |

##### <a name="if-using-private-dns-zones"></a>Při použití privátních zón DNS

Pokud používáte zóny DNS pro objekty BLOB a fronty, musíte nejdřív vytvořit tyto zóny DNS a později přidat požadované záznamy.

|Zóna |Služba  |
|---------|---------|
|`privatelink.blob.core.windows.net`     |  Objekt blob     |
|`privatelink.queue.core.windows.net`     | Fronta        |

V tuto chvíli vytvoříme při používání vlastních serverů DNS jenom zóny pro objekty BLOB a fronty. Přidání záznamů DNS se provede později ve dvou krocích:

1. Když zaregistrujete první instanci zálohování, to znamená při první konfiguraci zálohování
1. Při spuštění první zálohy

Tyto kroky provedeme v následujících oddílech.

## <a name="use-private-endpoints-for-backup"></a>Použití privátních koncových bodů pro zálohování

Po schválení privátních koncových bodů vytvořených pro trezor ve vaší virtuální síti je můžete začít používat pro zálohování a obnovení.

>[!IMPORTANT]
>Než budete pokračovat, ujistěte se, že jste úspěšně dokončili všechny kroky uvedené výše v dokumentu. K rekapitulace je nutné provést kroky v následujícím kontrolním seznamu:
>
>1. Vytvořil se (nový) Recovery Services trezor.
>2. Povolit trezor pro použití spravované identity přiřazené systémem
>3. Přiřazená příslušná oprávnění k spravované identitě trezoru
>4. Vytvořili jste privátní koncový bod pro svůj trezor.
>5. Schválen privátní koncový bod (Pokud není automaticky schválen)
>6. Všechny záznamy DNS jsou vhodně přidány (kromě záznamů objektů BLOB a front pro vlastní servery, které budou popsány v následujících částech).

### <a name="check-vm-connectivity"></a>Ověřit konektivitu virtuálních počítačů

V části virtuální počítač v uzamčené síti ověřte následující:

1. Virtuální počítač by měl mít přístup k AAD.
2. Spuštěním **příkazu nslookup** na adrese URL zálohy ( `xxxxxxxx.privatelink.<geo>.backup. windowsazure.com` ) z virtuálního počítače zajistěte připojení. To by mělo vrátit privátní IP adresu přiřazenou ve vaší virtuální síti.

### <a name="configure-backup"></a>Konfigurace zálohování

Po zajištění úspěšného dokončení výše uvedeného kontrolního seznamu a přístup k němu můžete pokračovat v konfiguraci zálohování úloh do trezoru. Pokud používáte vlastní server DNS, budete muset přidat záznamy DNS pro objekty BLOB a fronty, které jsou k dispozici po konfiguraci prvního zálohování.

#### <a name="dns-records-for-blobs-and-queues-only-for-custom-dns-servershost-files-after-the-first-registration"></a>Záznamy DNS pro objekty BLOB a fronty (jenom pro vlastní servery DNS/soubory hostitelů) po první registraci

Po nakonfigurování zálohy pro alespoň jeden prostředek v trezoru s povoleným privátním koncovým bodem přidejte požadované záznamy DNS pro objekty BLOB a fronty, jak je popsáno níže.

1. Přejděte do skupiny prostředků a vyhledejte privátní koncový bod, který jste vytvořili.
1. Kromě názvu privátního koncového bodu, který si využijete, se zobrazí dvě další privátní koncové body. Tyto začínají `<the name of the private endpoint>_ecs` na a jsou začínat příponou `_blob` a `_queue` v uvedeném pořadí.

    ![Prostředky privátního koncového bodu](./media/private-endpoints/private-endpoint-resources.png)

1. Přejděte ke každému z těchto privátních koncových bodů. V možnosti konfigurace DNS pro každý ze dvou privátních koncových bodů se zobrazí záznam s názvem a plně kvalifikovaným názvem domény a IP adresou. Kromě těch, které jsou popsány dříve, je přidejte do svého vlastního serveru DNS.
Pokud používáte hostitelský soubor, zajistěte odpovídající položky v souboru hostitele pro každý protokol IP nebo plně kvalifikovaný název domény v závislosti na následujícím formátu:

    `<private ip><space><blob service privatelink FQDN>`<br>
    `<private ip><space><queue service privatelink FQDN>`

    ![Konfigurace DNS objektu BLOB](./media/private-endpoints/blob-dns-configuration.png)

Kromě výše uvedeného je po prvním zálohování nutné zadat další položku, která je popsána [později](#dns-records-for-blobs-only-for-custom-dns-servershost-files-after-the-first-backup).

### <a name="backup-and-restore-of-workloads-in-azure-vm-sql-and-sap-hana"></a>Zálohování a obnovení úloh na virtuálním počítači Azure (SQL a SAP HANA)

Po vytvoření a schválení privátního koncového bodu se na straně klienta nevyžadují žádné další změny, aby bylo možné používat privátní koncový bod (Pokud nepoužíváte skupiny dostupnosti SQL, které probereme dále v této části). Veškerá komunikace a přenos dat ze zabezpečené sítě do trezoru se provede prostřednictvím privátního koncového bodu. Pokud ale po registraci serveru (SQL nebo SAP HANA) odeberete privátní koncové body pro trezor, budete muset kontejner znovu zaregistrovat do trezoru. Nemusíte pro ně zastavovat ochranu.

#### <a name="dns-records-for-blobs-only-for-custom-dns-servershost-files-after-the-first-backup"></a>Záznamy DNS pro objekty BLOB (pouze pro vlastní servery DNS/soubory hostitelů) po prvním zálohování

Po spuštění první zálohy a používání vlastního serveru DNS (bez podmíněného předávání) se pravděpodobně nezdaří zálohování. Pokud k tomu dojde:

1. Přejděte do skupiny prostředků a vyhledejte privátní koncový bod, který jste vytvořili.
1. Kromě tří privátních koncových bodů popsaných výše se teď zobrazí čtvrtý soukromý koncový bod s názvem začínajícím `<the name of the private endpoint>_prot` a s příponou `_blob` .

    ![Privátní endpoing s příponou "prot"](./media/private-endpoints/private-endpoint-prot.png)

1. Přejděte do tohoto nového privátního koncového bodu. V možnosti konfigurace DNS se zobrazí záznam s plně kvalifikovaným názvem domény a IP adresou. Přidejte je do privátního serveru DNS kromě těch popsaných výše.

    Pokud používáte hostitelský soubor, proveďte odpovídající položky v souboru hostitele pro každou IP adresu a plně kvalifikovaný název domény v závislosti na následujícím formátu:

    `<private ip><space><blob service privatelink FQDN>`

>[!NOTE]
>V tuto chvíli byste měli být schopni spustit nástroj **nslookup** z virtuálního počítače a po dokončení práce na záložních adresách úložiště a v úložištích znovu přeložit na privátní IP adresy.

### <a name="when-using-sql-availability-groups"></a>Při použití skupin dostupnosti SQL

Pokud používáte skupiny dostupnosti SQL (AG), budete muset zřídit podmíněné přesměrování ve vlastním DNS služby AG, jak je popsáno níže:

1. Přihlaste se k řadiči domény.
1. V rámci aplikace DNS přidejte v případě potřeby podmíněný předávací servery pro všechny tři zóny DNS (zálohování, objekty BLOB a fronty) na IP adresu hostitele 168.63.129.16 nebo vlastní IP adresu serveru DNS. Při předávání na IP adresu hostitele Azure se zobrazí následující snímky obrazovky. Pokud používáte vlastní server DNS, nahraďte IP adresou vašeho serveru DNS.

    ![Podmíněné servery pro přeposílání ve Správci DNS](./media/private-endpoints/dns-manager.png)

    ![Nové podmíněné dopředné](./media/private-endpoints/new-conditional-forwarder.png)

### <a name="backup-and-restore-through-mars-agent"></a>Zálohování a obnovení prostřednictvím agenta MARS

Při použití agenta MARS k zálohování místních prostředků se ujistěte, že vaše místní síť (obsahující vaše prostředky, které se mají zálohovat) má partnerský vztah s virtuální sítí Azure, která obsahuje privátní koncový bod pro trezor, takže ho můžete použít. Pak můžete pokračovat v instalaci agenta MARS a nakonfigurovat zálohování tak, jak je popsáno zde. Je však třeba zajistit, aby veškerá komunikace pro zálohování procházela pouze prostřednictvím partnerské sítě.

Pokud ale po registraci agenta MARS pro trezor odeberete privátní koncové body, budete muset kontejner znovu zaregistrovat do trezoru. Nemusíte pro ně zastavovat ochranu.

## <a name="deleting-private-endpoints"></a>Odstraňování privátních koncových bodů

V [této části](https://docs.microsoft.com/rest/api/virtualnetwork/privateendpoints/delete) se dozvíte, jak odstranit privátní koncové body.

## <a name="additional-topics"></a>Další témata

### <a name="create-a-recovery-services-vault-using-the-azure-resource-manager-client"></a>Vytvoření trezoru Recovery Services pomocí klienta Azure Resource Manager

Můžete vytvořit Recovery Services trezor a povolit jeho spravovanou identitu (vyžaduje se spravovaná identita, jak ho později ukážeme) pomocí klienta Azure Resource Manager. Ukázka pro tuto akci je sdílená níže:

```rest
armclient PUT /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>?api-version=2017-07-01-preview @C:\<filepath>\MSIVault.json
```

Výše uvedený soubor JSON by měl mít následující obsah:

JSON žádosti:

```json
{
  "location": "eastus2",
  "name": "<vaultname>",
  "etag": "W/\"datetime'2019-05-24T12%3A54%3A42.1757237Z'\"",
  "tags": {
    "PutKey": "PutValue"
  },
  "properties": {},
  "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>",
  "type": "Microsoft.RecoveryServices/Vaults",
  "sku": {
    "name": "RS0",
    "tier": "Standard"
  },
  "identity": {
    "type": "systemassigned"
  }
}
```

Odpověď JSON:

```json
{
   "location": "eastus2",
   "name": "<vaultname>",
   "etag": "W/\"datetime'2020-02-25T05%3A26%3A58.5181122Z'\"",
   "tags": {
     "PutKey": "PutValue"
   },
   "identity": {
     "tenantId": "<tenantid>",
     "principalId": "<principalid>",
     "type": "SystemAssigned"
   },
   "properties": {
     "provisioningState": "Succeeded",
     "privateEndpointStateForBackup": "None",
     "privateEndpointStateForSiteRecovery": "None"
   },
   "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>",
   "type": "Microsoft.RecoveryServices/Vaults",
   "sku": {
     "name": "RS0",
     "tier": "Standard"
   }
 }
```

>[!NOTE]
>Trezor vytvořený v tomto příkladu prostřednictvím klienta Azure Resource Manager je již vytvořen pomocí spravované identity přiřazené systémem.

### <a name="managing-permissions-on-resource-groups"></a>Správa oprávnění pro skupiny prostředků

Spravovaná identita úložiště musí mít ve skupině prostředků a ve virtuální síti, kde se vytvoří privátní koncové body, následující oprávnění:

- `Microsoft.Network/privateEndpoints/*` Tato operace je nutná k provedení operace CRUD u privátních koncových bodů ve skupině prostředků. Měl by se přiřadit ke skupině prostředků.
- `Microsoft.Network/virtualNetworks/subnets/join/action` To je vyžadováno ve virtuální síti, ve které se privátní IP adresa připojuje k privátnímu koncovému bodu.
- `Microsoft.Network/networkInterfaces/read` To je vyžadováno ve skupině prostředků, aby bylo možné získat síťové rozhraní vytvořené pro soukromý koncový bod.
- Role přispěvatele Privátní DNS zóny už tato role existuje a dá se použít k poskytnutí `Microsoft.Network/privateDnsZones/A/*` a `Microsoft.Network/privateDnsZones/virtualNetworkLinks/read` oprávnění.

K vytvoření rolí s požadovanými oprávněními můžete použít jednu z následujících metod:

#### <a name="create-roles-and-permissions-manually"></a>Ruční vytvoření rolí a oprávnění

Vytvořte následující soubory JSON a pomocí příkazu PowerShellu na konci oddílu vytvořte role:

PrivateEndpointContributorRoleDef.jsna

```json
{
  "Name": "PrivateEndpointContributor",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows management of Private Endpoint",
  "Actions": [
    "Microsoft.Network/privateEndpoints/*",
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

NetworkInterfaceReaderRoleDef.jsna

```json
{
  "Name": "NetworkInterfaceReader",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows read on networkInterfaces",
  "Actions": [
    "Microsoft.Network/networkInterfaces/read"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

PrivateEndpointSubnetContributorRoleDef.jsna

```json
{
  "Name": "PrivateEndpointSubnetContributor",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows adding of Private Endpoint connection to Virtual Networks",
  "Actions": [
    "Microsoft.Network/virtualNetworks/subnets/join/action"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

```azurepowershell
 New-AzRoleDefinition -InputFile "PrivateEndpointContributorRoleDef.json"
 New-AzRoleDefinition -InputFile "NetworkInterfaceReaderRoleDef.json"
 New-AzRoleDefinition -InputFile "PrivateEndpointSubnetContributorRoleDef.json"
```

#### <a name="use-a-script"></a>Použití skriptu

1. Spusťte **Cloud Shell** v Azure Portal a v okně PowerShellu vyberte **nahrát soubor** .

    ![Vybrat odeslat soubor v okně PowerShellu](./media/private-endpoints/upload-file-in-powershell.png)

1. Nahrajte tento skript: [VaultMsiPrereqScript](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/VaultMsiPrereqScript.ps1)

1. Přejít do domovské složky (například: `cd /home/user` )

1. Spusťte tento skript:

    ```azurepowershell
    ./VaultMsiPrereqScript.ps1 -subscription <subscription-Id> -vaultPEResourceGroup <vaultPERG> -vaultPESubnetResourceGroup <subnetRG> -vaultMsiName <msiName>
    ```

    Tyto parametry jsou:

    - **předplatné**: * * SubscriptionId, které má skupinu prostředků, ve které se vytvoří privátní koncový bod pro trezor, a podsíť, ve které se připojí privátní koncový bod trezoru.

    - **vaultPEResourceGroup**: Skupina prostředků, ve které se vytvoří privátní koncový bod úložiště.

    - **vaultPESubnetResourceGroup**: Skupina prostředků podsítě, ke které se připojí privátní koncový bod

    - **vaultMsiName**: název souboru MSI trezoru, který je stejný jako název **trezoru** .

1. Dokončete ověřování a skript převezme kontext daného předplatného, které jste zadali výše. Vytvoří příslušné role, pokud v tenantovi chybí, a přiřadí role ke službě MSI tohoto trezoru.

### <a name="creating-private-endpoints-using-azure-powershell"></a>Vytváření privátních koncových bodů pomocí Azure PowerShell

#### <a name="auto-approved-private-endpoints"></a>Automaticky schválené privátní koncové body

```azurepowershell
$vault = Get-AzRecoveryServicesVault `
        -ResourceGroupName $vaultResourceGroupName `
        -Name $vaultName
  
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
        -Name $privateEndpointConnectionName `
        -PrivateLinkServiceId $vault.ID `
        -GroupId "AzureBackup"  

$vnet = Get-AzVirtualNetwork -Name $vnetName -ResourceGroupName $VMResourceGroupName
$subnet = $vnet | Select -ExpandProperty subnets | Where-Object {$_.Name -eq '<subnetName>'}


$privateEndpoint = New-AzPrivateEndpoint `
        -ResourceGroupName $vmResourceGroupName `
        -Name $privateEndpointName `
        -Location $location `
        -Subnet $subnet `
        -PrivateLinkServiceConnection $privateEndpointConnection `
        -Force
```

### <a name="manual-approval-of-private-endpoints-using-the-azure-resource-manager-client"></a>Ruční schválení privátních koncových bodů pomocí klienta Azure Resource Manager

1. Pomocí **Gettrezoru** Získejte ID připojení privátního koncového bodu pro váš soukromý koncový bod.

    ```rest
    armclient GET /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/vaults/<vaultname>?api-version=2017-07-01-preview
    ```

    Tím se vrátí ID připojení privátního koncového bodu. Název připojení se dá načíst pomocí první části ID připojení, a to následujícím způsobem:

    `privateendpointconnectionid = {peName}.{vaultId}.backup.{guid}`

1. Získejte **ID připojení privátního koncového bodu** (a **název privátního koncového bodu** bez ohledu na to, kde je potřeba), a nahraďte ho v následujících JSON a Azure Resource Manager URI a zkuste změnit stav na "schváleno/odmítnuto/odpojeno", jak je znázorněno v následující ukázce:

    ```rest
    armclient PUT /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>/privateEndpointConnections/<privateendpointconnectionid>?api-version=2020-02-02-preview @C:\<filepath>\BackupAdminApproval.json
    ```

    JSON

    ```json
    {
    "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>/privateEndpointConnections/<privateendpointconnectionid>",
    "properties": {
        "privateEndpoint": {
        "id": "/subscriptions/<subscriptionid>/resourceGroups/<pergname>/providers/Microsoft.Network/privateEndpoints/pename"
        },
        "privateLinkServiceConnectionState": {
        "status": "Disconnected",  //choose state from Approved/Rejected/Disconnected
        "description": "Disconnected by <userid>"
        }
    }
    }
    ```

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

Otázka: Můžu vytvořit privátní koncový bod pro existující trezor služby Backup?<br>
A. Ne, privátní koncové body lze vytvořit pouze pro nové trezory služby Backup. Takže trezor nesmí mít v minulosti žádné chráněné položky. Před vytvořením privátních koncových bodů se ale nedají provést žádné pokusy o ochranu položek do trezoru.

Otázka: Pokusili jste se chránit položku do trezoru, ale nedošlo k jejímu uložení a trezor neobsahuje žádné chráněné položky. Můžu pro tento trezor vytvořit privátní koncové body?<br>
A. Ne, trezor nesmí mít žádné pokusy o ochranu jakýchkoli položek v minulosti.

Otázka: Mám trezor, který používá privátní koncové body pro zálohování a obnovení. Můžu později přidat nebo odebrat soukromé koncové body pro tento trezor i v případě, že jsou k němu chráněné zálohované položky?<br>
A. Ano. Pokud jste již vytvořili privátní koncové body pro trezor a chráněné zálohované položky, můžete později přidat nebo odebrat soukromé koncové body podle potřeby.

Otázka: Může být privátní koncový bod pro Azure Backup také použit pro Azure Site Recovery?<br>
A. Ne, privátní koncový bod pro zálohování se dá použít jenom pro Azure Backup. Pro Azure Site Recovery budete muset vytvořit nový privátní koncový bod, pokud ho služba podporuje.

Otázka: Vynechali jsme některý z kroků v tomto článku a zavedli jsme k ochraně zdroje dat. Můžu pořád používat privátní koncové body?<br>
A. Není nutné postupovat podle kroků uvedených v článku a nadále chránit položky může způsobit, že trezor nebude moci používat privátní koncové body. Proto doporučujeme, abyste před pokračováním v ochraně položek přeodkazovali na tento kontrolní seznam.

Otázka: Můžu použít vlastní server DNS místo používání privátní zóny DNS Azure nebo integrované privátní zóny DNS?<br>
A. Ano, můžete použít vlastní servery DNS. Zajistěte však, aby byly do této části přidány všechny požadované záznamy DNS jako navržené.

Otázka: Potřebuji na svém serveru provést další kroky, I když jsem použil postup v tomto článku?<br>
A. Po provedení postupu popsaného v tomto článku není nutné provádět další práci, aby bylo možné použít privátní koncové body pro zálohování a obnovení.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o všech [funkcích zabezpečení v Azure Backup](security-overview.md).

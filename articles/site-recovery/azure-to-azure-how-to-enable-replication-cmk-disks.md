---
title: Povolení replikace pro šifrované virtuální počítače Azure v Azure Site Recovery
description: Tento článek popisuje, jak nakonfigurovat replikaci pro virtuální počítače s klíčem spravovaným zákazníkem (CMK) povolené disky z jedné oblasti Azure do jiné pomocí site recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 01/10/2020
ms.author: mayg
ms.openlocfilehash: 367f29237a3f2a634f209026df47b0cbd6ffc97c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75897959"
---
# <a name="replicate-machines-with-customer-managed-keys-cmk-enabled-disks"></a>Replikovat počítače s disky spravovanými zákazníky (CMK)

Tento článek popisuje, jak replikovat virtuální počítače Azure s klíči spravovanými zákazníky (CMK) povolené spravované disky z jedné oblasti Azure do jiné.

## <a name="prerequisite"></a>Požadavek
Před povolením replikace pro virtuální počítače, které mají spravované disky s podporou CMK, je nutné vytvořit sady šifrování disků v cílové oblasti pro cílové předplatné.

## <a name="enable-replication"></a>Povolení replikace

V tomto příkladu je primární oblast Azure východní Asie a sekundární oblast je jihovýchodní Asie.

1. V úschovně vyberte **možnost +Replikovat**.
2. Poznamenejte si následující pole.
    - **Zdroj**: Místo původu virtuálních počítačích, což je v tomto případě **Azure**.
    - **Umístění zdroje:** Oblast Azure, kde chcete chránit vaše virtuální počítače. V tomto příkladu je zdrojové umístění "Východní Asie".
    - **Model nasazení**: Model nasazení Azure zdrojových počítačů.
    - **Zdrojové předplatné**: Předplatné, ke kterému patří vaše zdrojové virtuální počítače. Může se jedná o libovolné předplatné, které je ve stejném tenantovi služby Azure Active Directory jako trezor služeb pro obnovení.
    - **Skupina prostředků:** Skupina prostředků, do které patří vaše zdrojové virtuální počítače. Všechny virtuální ho disponiál ve vybrané skupině prostředků jsou uvedeny pro ochranu v dalším kroku.

3. Ve **virtuálních počítačích** > **Vyberte virtuální počítače**vyberte každý virtuální počítač, který chcete replikovat. Můžete vybrat pouze počítače, pro které je možné povolit replikaci. Potom vyberte **OK**.

4. V **nastavení**můžete nakonfigurovat následující nastavení cílového webu.

    - **Cílové umístění:** Umístění, kde budou replikována data zdrojového virtuálního počítače. Site Recovery poskytuje seznam vhodných cílových oblastí na základě umístění vybraného počítače. Doporučujeme použít stejné umístění jako umístění trezoru služby Recovery Services.
    - **Cílové předplatné**: Cílové předplatné, které se používá pro zotavení po havárii. Ve výchozím nastavení je cílové předplatné stejné jako zdrojové předplatné.
    - **Cílová skupina prostředků**: Skupina prostředků, do které patří všechny replikované virtuální počítače. Ve výchozím nastavení site recovery vytvoří novou skupinu prostředků v cílové oblasti. Název získá `asr` příponu. Pokud skupina prostředků již existuje, která byla vytvořena azure site recovery, je znovu použít. Můžete také přizpůsobit, jak je znázorněno v následující části. Umístění cílové skupiny prostředků může být libovolné oblasti Azure s výjimkou oblasti, kde jsou hostované zdrojové virtuální počítače.
    - **Cílová virtuální síť**: Ve výchozím nastavení site recovery vytvoří novou virtuální síť v cílové oblasti. Název získá `asr` příponu. Je mapován do zdrojové sítě a používán pro budoucí ochranu. [Přečtěte si další informace](site-recovery-network-mapping-azure-to-azure.md) o mapování sítě.
    - **Cílové účty úložiště (pokud váš zdrojový virtuální počítač nepoužívá spravované disky)**: Ve výchozím nastavení site recovery vytvoří nový cílový účet úložiště napodobováním konfigurace úložiště zdrojového virtuálního počítače. Pokud účet úložiště již existuje, je znovu použit.
    - **Replika spravované disky (pokud váš zdrojový virtuální počítač používá spravované disky):** Site Recovery vytvoří nové repliky spravované disky v cílové oblasti zrcadlit zdrojové virtuální počítače spravované disky stejného typu úložiště (standardní nebo premium) jako zdrojové spravované disky virtuálního počítače.
    - **Účty úložiště mezipaměti**: Site Recovery potřebuje další účet úložiště s názvem *úložiště mezipaměti* ve zdrojové oblasti. Všechny změny na zdrojových virtuálních počítačích jsou sledovány a odeslány do účtu úložiště mezipaměti. Pak jsou replikovány do cílového umístění.
    - **Dostupnost :** Ve výchozím nastavení site recovery vytvoří novou sadu dostupnosti v cílové oblasti. Název má `asr` příponu. Pokud již existuje sada dostupnosti, která byla vytvořena službou Site Recovery, je znovu použita.
    - **Sady šifrování disků (DES):** Site Recovery potřebuje sadu diskových šifrovacích sad, která se použijí pro repliky a cílové spravované disky. Před povolením replikace je nutné předem vytvořit DES v cílovém předplatném a cílové oblasti. Ve výchozím nastavení není vybrána možnost DES. Musíte kliknout na 'Přizpůsobit' vybrat DES na zdrojový disk.
    - **Zásady replikace**: Definuje nastavení pro historii uchovávání bodů obnovení a frekvenci snímků konzistentních s aplikací. Ve výchozím nastavení site recovery vytvoří nové zásady replikace s výchozím nastavením *24 hodin* pro uchovávání bodů obnovení a *60 minut* pro frekvenci snímků konzistentních s aplikací.

    ![Povolit replikaci pro počítač s disky s povolenou SADOU CMK](./media/azure-to-azure-how-to-enable-replication-cmk-disks/cmk-enable-dr.png)

## <a name="customize-target-resources"></a>Přizpůsobení cílových zdrojů

Chcete-li upravit výchozí cílové nastavení obnovení webu, postupujte takto.

1. Chcete-li upravit výchozí cílové předplatné, vyberte **možnost Přizpůsobit** vedle možnosti Cílové předplatné. Vyberte předplatné ze seznamu předplatných, které jsou k dispozici v tenantovi Azure AD.

2. Chcete-li změnit následující výchozí nastavení, vyberte **možnost Přizpůsobit** vedle položky Skupiny prostředků, Sítě, Úložiště a Skupiny dostupnosti:
    - V **části Cílová skupina prostředků**vyberte skupinu prostředků ze seznamu skupin prostředků v cílovém umístění předplatného.
    - V **části Cílová virtuální síť**vyberte síť ze seznamu virtuálních sítí v cílovém umístění.
    - Pro **sadu dostupnosti**můžete přidat nastavení sady dostupnosti do virtuálního počítače, pokud jsou součástí skupiny dostupnosti ve zdrojové oblasti.
    - U **účtů cílového úložiště**vyberte účet, který chcete použít.

3. Chcete-li vybrat cílový des pro každý zdrojový spravovaný disk se správou spravovaného klíče spravovaného zákazníkem (CMK), vyberte možnost **Přizpůsobit** vedle možnosti Nastavení šifrování úložiště. V době výběru budete také moci zobrazit, ke kterému cílovému trezoru klíčů je des přidružen.

4. Vyberte **možnost Vytvořit cílový prostředek** > **Povolit replikaci**.
5. Po virtuálních disponibilní pro replikaci, můžete zkontrolovat stav virtuálních zařízení v části **Replikované položky**.

![Povolit replikaci pro počítač s disky s povolenou SADOU CMK](./media/azure-to-azure-how-to-enable-replication-cmk-disks/cmk-customize-target-disk-properties.png)

>[!NOTE]
>Během počáteční replikace může aktualizace stavu nějakou dobu trvat bez zjevného průběhu. Chcete-li získat nejnovější stav, klepněte na tlačítko **Aktualizovat.**

## <a name="faqs"></a>Nejčastější dotazy

* Povolit CMK na existující replikované položky, jak lze zajistit, že CMK je použita na cílové oblasti stejně?

    Můžete zjistit název spravovaného disku repliky (vytvořený azure site recovery v cílové oblasti) a připojit DES k tomuto disku repliky. Podrobnosti DES však po připojení neuvidíte v okně Disky. Případně můžete zakázat replikaci virtuálního virtuálního serveru a znovu ho povolit. Zajistí, že se v okně Disky pro replikovanou položku zobrazí podrobnosti des a trezoru klíčů.

* Přidal jsem nový disk s podporou CMK do replikované položky. Jak můžu replikovat tento disk pomocí Azure Site Recovery?

    Přidání nového disku s podporou CMK k existující replikované položce není podporováno. Zakažte replikaci a znovu povolte replikaci pro virtuální počítač.


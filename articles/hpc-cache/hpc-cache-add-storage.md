---
title: Přidání úložiště do mezipaměti Azure HPC
description: Jak definovat cíle úložiště tak, aby vaše azure hpc cache můžete použít místní systém souborů NFS nebo kontejnery objektů Blob Azure pro dlouhodobé úložiště souborů
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: rohogue
ms.openlocfilehash: 3fbc4e683c2b0e72c3a084a59793dbf9eb4b658c
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657405"
---
# <a name="add-storage-targets"></a>Přidání cílů úložiště

*Cíle úložiště* jsou back-endové úložiště pro soubory, ke kterým se přistupuje prostřednictvím mezipaměti Azure HPC. Úložiště systému souborů NFS (například místní hardwarový systém) můžete přidat nebo ukládat data v objektu Blob Azure.

Pro jednu mezipaměť můžete definovat až deset různých cílů úložiště. Mezipaměť představuje všechny cíle úložiště v jednom agregovaném oboru názvů.

Nezapomeňte, že exportúložiště musí být přístupný z virtuální sítě mezipaměti. Pro místní hardwarové úložiště může být nutné nastavit server DNS, který dokáže vyřešit názvy hostitelů pro přístup k úložišti systému souborů NFS. Přečtěte si více v [dns přístupu](hpc-cache-prereqs.md#dns-access).

Po vytvoření mezipaměti přidejte cíle úložiště. Postup se mírně liší v závislosti na tom, zda přidáváte úložiště objektů blob Azure nebo export systému nfs. Podrobnosti pro každého jsou uvedeny níže.

## <a name="open-the-storage-targets-page"></a>Otevření stránky cílů úložiště

Na portálu Azure otevřete instanci mezipaměti a klikněte na **cíle úložiště** na levém postranním panelu. Stránka cílů úložiště obsahuje seznam všech existujících cílů a poskytuje odkaz na přidání nového.

![Snímek obrazovky s odkazem cíle úložiště na postranním panelu pod nadpisem Konfigurovat, který je mezi nadpisy kategorií Nastavení a monitorování](media/hpc-cache-storage-targets-sidebar.png)

## <a name="add-a-new-azure-blob-storage-target"></a>Přidání nového cíle úložiště objektů blob Azure

Nový cíl úložiště objektů Blob potřebuje prázdný kontejner objektů Blob nebo kontejner, který je naplněn daty ve formátu cloudového systému souborů Azure HPC Cache. Přečtěte si další informace o předběžném načítání kontejneru objektů Blob v [přesunutí dat do úložiště objektů Blob Azure](hpc-cache-ingest.md).

Z této stránky můžete vytvořit nový kontejner těsně před jeho přidáním.

![snímek obrazovky s cílovou stránkou úložiště pro přidání, naplněnou informacemi o novém cíli úložiště objektů blob Azure](media/hpc-cache-add-blob.png)

Chcete-li definovat kontejner objektů blob Azure, zadejte tyto informace.

* **Název cíle úložiště** – nastavte název, který identifikuje tento cíl úložiště v mezipaměti Azure HPC.
* **Typ cíle** – zvolte **objekt blob**.
* **Účet úložiště** – vyberte účet, který chcete použít.

  Budete muset autorizovat instanci mezipaměti pro přístup k účtu úložiště, jak je popsáno v [přidat role přístupu](#add-the-access-control-roles-to-your-account).

  Informace o druhu účtu úložiště, který můžete použít, najdete v části [Požadavky na úložiště objektů Blob](hpc-cache-prereqs.md#blob-storage-requirements).

* **Kontejner úložiště** – vyberte kontejner objektů Blob pro tento cíl nebo klepněte na **tlačítko Vytvořit nový**.

  ![snímek obrazovky dialogového okna pro určení názvu a úrovně přístupu (soukromé) pro nový kontejner](media/add-blob-new-container.png)

* **Cesta virtuálního oboru názvů** – Nastavte cestu k souboru směřující ke klientovi pro tento cíl úložiště. Další informace o funkci virtuálního oboru názvů najdete v [části Konfigurace agregovaného oboru](hpc-cache-namespace.md) názvů.

Po dokončení přidejte cíl úložiště kliknutím na **OK.**

> [!NOTE]
> Pokud je brána firewall účtu úložiště nastavená tak, aby omezovala přístup pouze k "vybraným sítím", použijte dočasné řešení zdokumentované v [části Řešení nastavení brány firewall úložiště objektů Blob](hpc-cache-blob-firewall-fix.md).

### <a name="add-the-access-control-roles-to-your-account"></a>Přidání rolí řízení přístupu do účtu

Azure HPC Cache používá [řízení přístupu na základě rolí (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/index) k autorizaci služby mezipaměti pro přístup k účtu úložiště pro cíle úložiště objektů blob Azure.

Vlastník účtu úložiště musí explicitně přidat role [přispěvatele účtu úložiště](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-contributor) a [přispěvatele dat objektů blob úložiště](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) pro uživatele "Zprostředkovatel prostředků mezipaměti HPC".

Můžete to udělat předem nebo kliknutím na odkaz na stránce, kde přidáte cíl úložiště objektů Blob. Mějte na paměti, že může trvat až pět minut, než se nastavení rolí rozšíří v prostředí Azure, takže byste měli počkat několik minut po přidání rolí před vytvořením cíle úložiště.

Postup přidání rolí RBAC:

1. Otevřete stránku **řízení přístupu (IAM)** pro účet úložiště. (Odkaz na **stránce Přidat cíl úložiště** automaticky otevře tuto stránku pro vybraný účet.)

1. Klikněte **+** na horní část stránky a zvolte **Přidat přiřazení role**.

1. V seznamu vyberte roli "Přispěvatel účtu úložiště".

1. V poli **Přiřadit přístup k** ponechte vybranou výchozí hodnotu ("Uživatel, skupina nebo instanční objekt Azure AD").  

1. V poli **Vybrat** vyhledejte "hpc".  Tento řetězec by měl odpovídat jednomu instančnímu objektu s názvem "Zprostředkovatel prostředků mezipaměti HPC". Klikněte na tento objekt zabezpečení a vyberte jej.

   > [!NOTE]
   > Pokud hledání "hpc" nefunguje, zkuste místo toho použít řetězec "storagecache". Uživatelé, kteří se účastnili náhledů (před GA), možná budou muset použít starší název instančního objektu.

1. Klikněte dole na tlačítko **Uložit.**

1. Tento postup opakujte, chcete-li přiřadit roli "Přispěvatel dat objektů blob úložiště".  

![snímek obrazovky s grafickým rozhraním přiřazení role přidání](media/hpc-cache-add-role.png)

## <a name="add-a-new-nfs-storage-target"></a>Přidání nového cíle úložiště systému nfs

Cíl úložiště systému nfs má více polí než cíl úložiště objektů blob. Tato pole určují, jak dosáhnout exportu úložiště a jak efektivně ukládat data do mezipaměti. Cíl úložiště systému souborů NFS také umožňuje vytvořit více cest oboru názvů, pokud má hostitel systému souborů NFS k dispozici více než jeden export.

![Snímek obrazovky s cílovou stránkou přidání úložiště s definovaným cílem systému sdílení nfs](media/add-nfs-target.png)

> [!NOTE]
> Před vytvořením cíle úložiště systému souborů NFS se ujistěte, že váš úložný systém je přístupný z mezipaměti Azure HPC a splňuje požadavky na oprávnění. Vytvoření cíle úložiště se nezdaří, pokud mezipaměť nemá přístup k systému úložiště. Podrobnosti najdete [v požadavcích na úložiště systému NFS](hpc-cache-prereqs.md#nfs-storage-requirements) [a při potížích s řešením konfigurace na serveru NAS a s cílem úložiště systému NFS.](troubleshoot-nas.md)

Zadejte tyto informace pro cíl úložiště podporovaného systémem systému sdílení nfs:

* **Název cíle úložiště** – nastavte název, který identifikuje tento cíl úložiště v mezipaměti Azure HPC.

* **Typ cíle** – zvolte **systém nfs**.

* **Hostname** - Zadejte IP adresu nebo plně kvalifikovaný název domény pro váš systém úložiště nfs. (Název domény použijte pouze v případě, že vaše mezipaměť má přístup k serveru DNS, který může název přeložit.)

* **Model použití** – Zvolte jeden z profilů ukládání dat do mezipaměti na základě pracovního postupu, který je popsán v části [Zvolte model použití](#choose-a-usage-model), níže.

### <a name="nfs-namespace-paths"></a>Cesty oboru názvů nfs

Cíl úložiště systému souborů NFS může mít více virtuálních cest, pokud každá cesta představuje jiný export nebo podadresář ve stejném systému úložiště.

Vytvořte všechny cesty z jednoho cíle úložiště.

Cesty [oboru názvů](hpc-cache-edit-storage.md) můžete kdykoli přidat a upravit na cíl úložiště.

Vyplňte tyto hodnoty pro každou cestu oboru názvů:

* **Cesta virtuálního oboru názvů** – Nastavte cestu k souboru směřující ke klientovi pro tento cíl úložiště. Další informace o funkci virtuálního oboru názvů najdete v [části Konfigurace agregovaného oboru](hpc-cache-namespace.md) názvů.

<!--  The virtual path should start with a slash ``/``. -->

* **Cesta exportu nfs** - Zadejte cestu k exportu nfs.

* **Cesta podadresáře** - Chcete-li připojit určitý podadresář exportu, zadejte jej sem. Pokud ne, ponechte toto pole prázdné.

Po dokončení přidejte cíl úložiště kliknutím na **OK.**

### <a name="choose-a-usage-model"></a>Výběr modelu použití
<!-- referenced from GUI - update aka.ms link if you change this heading -->

Když vytvoříte cíl úložiště, který odkazuje na systém úložiště systému souborů NFS, musíte zvolit model využití pro tento cíl. Tento model určuje, jak jsou data ukládána do mezipaměti.

Existují tři možnosti:

* **Čtení těžkých, zřídka zápisů** – tuto možnost použijte, pokud chcete urychlit přístup pro čtení k souborům, které jsou statické nebo zřídka změněné.

  Tato možnost ukládá soubory, které klienti čtou, ale okamžitě předává zápisy do back-endového úložiště. Soubory uložené v mezipaměti nejsou nikdy porovnány se soubory na svazku úložiště systému souborů NFS.

  Tuto možnost nepoužívejte, pokud existuje riziko, že soubor může být změněn přímo v systému úložiště, aniž by jej nejprve zapisovali do mezipaměti. Pokud k tomu dojde, verze souboru uložená v mezipaměti nebude nikdy aktualizována změnami ze zadního konce a sada dat může být nekonzistentní.

* **Větší než 15% zápisy** - Tato možnost urychluje výkon čtení i zápisu. Při použití této možnosti musí všichni klienti přistupovat k souborům prostřednictvím mezipaměti Azure HPC namísto přímého připojení back-endového úložiště. Soubory uložené v mezipaměti budou mít nedávné změny, které nejsou uloženy v back-endu.

  V tomto modelu použití soubory v mezipaměti nejsou kontrolovány proti soubory na back-end úložiště. Předpokládá se, že verze souboru uložená v mezipaměti je aktuálnější. Změněný soubor v mezipaměti je zapsán do systému úložiště back-end poté, co byl v mezipaměti po dobu jedné hodiny bez dalších změn.

* **Klienti zapisují do cíle systému souborů NFS, obchází mezipaměť** – tuto možnost zvolte, pokud všichni klienti ve vašem pracovním postupu zapisují data přímo do úložného systému, aniž by nejprve zapisují do mezipaměti. Soubory, které klienti požadují, jsou uloženy do mezipaměti, ale všechny změny těchto souborů z klienta jsou okamžitě předány zpět do systému úložiště back-end.

  S tímto modelem použití jsou soubory v mezipaměti často kontrolovány proti back-endverze pro aktualizace. Toto ověření umožňuje měnit soubory mimo mezipaměť při zachování konzistence dat.

Tato tabulka shrnuje rozdíly v modelu použití:

| Model použití | Režim ukládání do mezipaměti | Ověření back-endu | Maximální zpoždění zpětného zápisu |
| ---- | ---- | ---- | ---- |
| Přečtěte si těžké, řídké zápisy | Čtení | Never (Nikdy) | Žádný |
| Více než 15% píše | Čtení/zápis | Never (Nikdy) | 1 hodina |
| Klienti obejít mezipaměť | Čtení | 30 sekund | Žádný |

## <a name="next-steps"></a>Další kroky

Po vytvoření cílů úložiště zvažte jeden z těchto úkolů:

* [Připojení mezipaměti Azure HPC](hpc-cache-mount.md)
* [Přesunutí dat do úložiště objektů blob Azure](hpc-cache-ingest.md)

Pokud potřebujete aktualizovat všechna nastavení, můžete [upravit cíl úložiště](hpc-cache-edit-storage.md).

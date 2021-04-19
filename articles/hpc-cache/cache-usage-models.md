---
title: Modely využití mezipaměti HPC Azure
description: Popisuje různé modely využití mezipaměti a jejich výběr mezi nimi pro nastavení ukládání do mezipaměti jen pro čtení nebo čtení a zápis a řízení dalších nastavení ukládání do mezipaměti.
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 04/08/2021
ms.author: v-erkel
ms.openlocfilehash: 7e1b11fd15cca9b11fc627222318f08d31743336
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2021
ms.locfileid: "107719182"
---
# <a name="understand-cache-usage-models"></a>Vysvětlení modelů využití mezipaměti

Modely využití mezipaměti umožňují přizpůsobit způsob, jakým mezipaměť prostředí Azure HPC ukládá soubory pro urychlení pracovního postupu.

## <a name="basic-file-caching-concepts"></a>Základní koncepty ukládání souborů do mezipaměti

Ukládání souborů do mezipaměti je způsob, jakým Azure HPC mezipaměť zrychluje požadavky klientů. Používá tyto základní postupy:

* Mezipaměť **pro čtení** – Azure HPC cache uchovává kopii souborů, které si klienti vyžádají ze systému úložiště. Když klient příště požádá o stejný soubor, mezipaměť HPC může poskytnout verzi ve své mezipaměti, místo aby se musela znovu načíst z back-endového systému úložiště.

* **Ukládání do mezipaměti** – volitelně mezipaměť HPC Azure může ukládat kopii všech změněných souborů odeslaných z klientských počítačů. Pokud více klientů provádí v krátkém období změny ve stejném souboru, mezipaměť může shromáždit všechny změny v mezipaměti, aniž byste museli psát každou změnu jednotlivě do back-endového systému úložiště.

  Po zadané době bez jakýchkoli změn mezipaměť přesune soubor do dlouhodobého systému úložiště.

  Pokud je ukládání do mezipaměti pro zápis zakázané, mezipaměť neuloží změněný soubor a hned ho zapíše do back-endového systému úložiště.

* **Zpoždění zpětného zápisu** – u mezipaměti s povoleným ukládáním do mezipaměti pro zápis je zpoždění proti zápisu za dobu, po kterou mezipaměť čeká na další změny souborů před kopírováním souboru do back-endového systému úložiště.

* **Ověření back-endu** – nastavení ověřování back-endu určuje, jak často mezipaměť porovnává svou místní kopii souboru se vzdálenou verzí v systému back-end úložiště. Pokud je back-end kopie novější než kopie uložená v mezipaměti, mezipaměť načte vzdálenou kopii a uloží ji pro budoucí požadavky.

  Nastavení ověřování back-endu ukazuje, kdy mezipaměť *automaticky* porovnává své soubory se zdrojovými soubory ve vzdáleném úložišti. Mezipaměť prostředí Azure HPC však můžete vynutit k porovnání souborů pomocí operace adresáře, která obsahuje požadavek READDIRPLUS. READDIRPLUS je standardní rozhraní API pro systém souborů NFS (označované také jako rozšířené čtení), které vrací metadata adresáře, což způsobí, že mezipaměť porovná a aktualizuje soubory.

Modely využití integrované do mezipaměti HPC Azure mají pro tato nastavení jiné hodnoty, takže si můžete vybrat nejvhodnější kombinaci pro vaši situaci.

## <a name="choose-the-right-usage-model-for-your-workflow"></a>Volba správného modelu použití pro váš pracovní postup

Pro každý cíl úložiště protokolu NFS, který používáte, musíte zvolit model využití. Cíle Azure Blob Storage mají integrovaný model použití, který nejde přizpůsobit.

Modely využití mezipaměti HPC vám umožní zvolit způsob vyrovnávání rychlé odezvy s rizikem při získávání zastaralých dat. Chcete-li optimalizovat rychlost čtení souborů, nesmíte se starat o to, zda jsou soubory v mezipaměti zkontrolovány proti back-endové soubory. Na druhé straně, pokud chcete mít jistotu, že jsou soubory vždycky aktuální se vzdáleným úložištěm, vyberte model, který se často kontroluje.

Toto jsou možnosti modelu použití:

* **Čtení těžkých, zřídka používaných zápisů** – tuto možnost použijte, pokud chcete zrychlit přístup pro čtení souborů, které jsou statické nebo zřídka změněné.

  Tato možnost ukládá do mezipaměti čtení klienta, ale nezapisuje do mezipaměti. Okamžitě projde zápisy do back-endu úložiště.
  
  Soubory uložené v mezipaměti nejsou automaticky porovnány se soubory na svazku úložiště NFS. (Přečtěte si popis ověření back-endu výše a zjistěte, jak je porovnat ručně.)

  Tuto možnost nepoužívejte, pokud existuje riziko, že soubor může být upraven přímo v systému úložiště, aniž byste ho nejdřív napsali do mezipaměti. Pokud k tomu dojde, verze souboru v mezipaměti nebude synchronizována s back-end souborem.

* Více **než 15% zápisů** – Tato možnost zrychluje výkon čtení i zápisu. Při použití této možnosti musí mít všichni klienti přístup k souborům přes mezipaměť prostředí Azure HPC místo přímého připojení k úložišti back-endu. Soubory v mezipaměti budou mít poslední změny, které ještě nebyly zkopírovány do back-endu.

  V tomto modelu použití jsou soubory v mezipaměti kontrolovány pouze proti souborům v úložišti back-endu každých 8 hodin. Předpokládá se, že verze souboru v mezipaměti je aktuálnější. Upravený soubor v mezipaměti se zapisuje do back-endového systému úložiště po dobu 20 minut v mezipaměti.<!-- an hour --> bez dalších změn.

* **Klienti zapisují do cíle NFS, vynechá mezipaměť** – tuto možnost vyberte, pokud klienti v pracovním postupu zapisují data přímo do systému úložiště, aniž by museli nejdřív zapisovat do mezipaměti, nebo pokud chcete optimalizovat konzistenci dat. Soubory, které jsou požadavky klientů ukládány do mezipaměti (čtení), ale všechny změny těchto souborů z klienta (zápisy) nejsou ukládány do mezipaměti. Jsou předávány přímo do back-endového systému úložiště.

  V tomto modelu použití jsou soubory v mezipaměti často kontrolovány proti verzím back-endu pro aktualizace – každých 30 sekund. Toto ověření umožňuje změnu souborů mimo mezipaměť při zachování konzistence dat.

  > [!TIP]
  > Tyto první tři základní modely použití lze použít ke zpracování většiny pracovních postupů Azure HPC cache. Další možnosti jsou pro méně běžné scénáře.

* Více **než 15% zápisů, kontrola záložního serveru na změny každých 30 sekund** a více **než 15% zápisů, kontrola, zda je server pro změny každých 60 sekund** . tyto možnosti jsou navrženy pro pracovní postupy, ve kterých chcete urychlit čtení i zápisy, ale existuje možnost, že jiný uživatel bude zapisovat přímo do back-endového systému úložiště. Pokud například více sad klientů pracuje na stejných souborech z různých umístění, mohou tyto modely využití vyvážit rovnováhu mezi nutností rychlého přístupu k souborům s nízkou tolerancí pro zastaralý obsah ze zdroje.

* Více **než 15% zápisů, zápis zpět na server každých 30 sekund** – Tato možnost je určená pro scénář, ve kterém více klientů aktivně mění stejné soubory, nebo pokud někteří klienti přistupují k úložišti back-endu přímo místo připojení mezipaměti.

  Časté zápisy back-end ovlivňují výkon mezipaměti, takže byste měli zvážit použití modelu **více než 15% zápisů** využití, pokud dojde ke konfliktu souborů, například pokud víte, že různí klienti pracují v různých oblastech stejné sady souborů.

* **Čtení je těžké, probíhá kontrola záložního serveru každé 3 hodiny** – Tato možnost má přednost při rychlém čtení na straně klienta, ale také pravidelně obnovuje soubory v mezipaměti z back-endového systému úložiště, a to na rozdíl od paměti **pro čtení a zřídka používaných zápisů** .

Tato tabulka shrnuje rozdíly v modelu použití:

[!INCLUDE [usage-models-table.md](includes/usage-models-table.md)]

Pokud máte dotazy týkající se modelu nejlepšího využití pro pracovní postup Azure HPC cache, kontaktujte svého zástupce Azure nebo otevřete žádost o podporu pro pomoc.

## <a name="know-when-to-remount-clients-for-nlm"></a>Informace o tom, kdy se mají znovu připojit klienti pro NLM

V některých situacích může být nutné znovu připojit klienty, pokud změníte model využití cíle úložiště. To je potřeba kvůli způsobu, jakým modely použití zpracovávají požadavky nástroje Network Lock Manager (NLM).

Mezipaměť HPC je mezi klienty a back-end systémem úložiště. Mezipaměť obvykle projde požadavky NLM do back-endového systému úložiště, ale v některých případech samotné mezipaměti potvrdí požadavek NLM a vrátí hodnotu klientovi. V mezipaměti HPC Azure se to děje jenom v případě, že používáte model využití **těžkých silných, zřídka se zápisy** (nebo se standardním cílem úložiště BLOB, který nemá konfigurovatelné modely využití).

Došlo k malému riziku konfliktu souborů, pokud změníte mezi modelem použití s **nečastými zápisy** a různými modely využití. Neexistuje žádný způsob, jak přenést aktuální stav NLM z mezipaměti do systému úložiště nebo naopak. Proto je stav zámku klienta nepřesný.

Znovu připojte klienty, abyste měli jistotu, že mají správný stav NLM s novým správcem zámků.

Pokud vaši klienti odesílají požadavek NLM, když model využití nebo back-end úložiště ho nepodporuje, zobrazí se mu chyba.

### <a name="disable-nlm-at-client-mount-time"></a>Zakázat NLM v době připojení klienta

Bez ohledu na to, jestli vaše klientské systémy odesílají požadavky NLM, není vždycky snadné.

Ověřování NLM můžete zakázat, pokud klienti připojí cluster pomocí možnosti ``-o nolock`` v ``mount`` příkazu.

Přesné chování ``nolock`` možnosti závisí na klientském operačním systému, proto si projděte dokumentaci ke službě Mount (muž 5 NFS) pro klientský operační systém. Ve většině případů se zámek přesune místně do klienta. Pokud vaše aplikace zamkne soubory mezi více klienty, buďte opatrní.

> [!NOTE]
> ADLS – systém souborů NFS nepodporuje NLM. Při použití cíle úložiště ADLS se systémem souborů NFS byste měli vypnout ověřování NLM pomocí možnosti připojit výše.

## <a name="next-steps"></a>Další kroky

* [Přidání cílů úložiště](hpc-cache-add-storage.md) do mezipaměti HPC Azure

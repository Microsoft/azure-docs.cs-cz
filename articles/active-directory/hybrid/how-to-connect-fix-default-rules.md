---
title: Jak opravit upravená výchozí pravidla – Azure AD Connect | Dokumenty společnosti Microsoft
description: Přečtěte si, jak opravit upravená výchozí pravidla, která jsou dodávána s Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
editor: curtand
ms.reviewer: darora10
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/21/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4626e0149028a140d143fb8d0969a03b732201fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79036980"
---
# <a name="fix-modified-default-rules-in-azure-ad-connect"></a>Oprava změněných výchozích pravidel ve službě Azure AD Connect

Azure Active Directory (Azure AD) Connect používá výchozí pravidla pro synchronizaci.  Bohužel, tato pravidla se nevztahují univerzálně na všechny organizace. Na základě vašich požadavků je možná nutné je upravit. Tento článek popisuje dva příklady nejběžnějších úprav a vysvětluje správný způsob, jak dosáhnout těchto úprav.

>[!NOTE] 
> Úprava existujících výchozích pravidel za účelem dosažení potřebného vlastního nastavení není podporována. Pokud tak učiníte, zabrání aktualizaci těchto pravidel na nejnovější verzi v budoucích verzích. Nebudete mít potřebné opravy chyb nebo nové funkce. Tento dokument vysvětluje, jak dosáhnout stejného výsledku bez změny existujících výchozích pravidel. 

## <a name="how-to-identify-modified-default-rules"></a>Jak identifikovat upravená výchozí pravidla
Počínaje verzí 1.3.7.0 služby Azure AD Connect je snadné identifikovat upravené výchozí pravidlo. Přejděte do **aplikace na ploše**a vyberte Editor pravidel **synchronizace**.

![Azure AD Connect se zvýrazněným editorem pravidel synchronizace](media/how-to-connect-fix-default-rules/default1.png)

V editoru jsou všechna upravená výchozí pravidla zobrazena s výstražnou ikonou před názvem.

![Ikona upozornění](media/how-to-connect-fix-default-rules/default2.png)

 Zobrazí se také zakázané pravidlo se stejným názvem (toto je standardní výchozí pravidlo).

![Editor pravidel synchronizace, zobrazující standardní výchozí pravidlo a upravené výchozí pravidlo](media/how-to-connect-fix-default-rules/default2a.png)

## <a name="common-customizations"></a>Běžná vlastní nastavení
Následují běžná vlastní nastavení výchozích pravidel:

- Změnit tok atributů
- Změnit filtr oboru
- Změnit podmínku spojení

Před změnou pravidel:

- Zakažte plánovač synchronizace. Plánovač běží ve výchozím nastavení každých 30 minut. Ujistěte se, že se nespouští, když provádíte změny a řešíte nové předpisy. Chcete-li plánovač dočasně zakázat, spusťte prostředí PowerShell a spusťte program `Set-ADSyncScheduler -SyncCycleEnabled $false`.
 ![Příkazy prostředí PowerShell pro zakázání plánovače synchronizace](media/how-to-connect-fix-default-rules/default3.png)

- Změna filtru oborů může mít za následek odstranění objektů v cílovém adresáři. Buďte opatrní před provedením jakýchkoli změn v vymezení objektů. Doporučujeme provést změny na pracovní server před provedením změn na aktivním serveru.
- Spusťte náhled na jeden objekt, jak je uvedeno v části [Ověřit pravidlo synchronizace,](#validate-sync-rule) po přidání nového pravidla.
- Po přidání nového pravidla nebo úpravě vlastního pravidla synchronizace spusťte úplnou synchronizaci. Tato synchronizace platí nová pravidla pro všechny objekty.

## <a name="change-attribute-flow"></a>Změnit tok atributů
Existují tři různé scénáře pro změnu toku atributů:
- Přidání nového atributu.
- Přepsání hodnoty existujícího atributu.
- Volba nesynchronizovat existující atribut.

Můžete to provést bez změny standardních výchozích pravidel.

### <a name="add-a-new-attribute"></a>Přidání nového atributu
Pokud zjistíte, že atribut neteče ze zdrojového adresáře do cílového adresáře, použijte k opravě [rozšíření Azure AD Connect: Directory.](how-to-connect-sync-feature-directory-extensions.md)

Pokud rozšíření nefungují pro vás, zkuste přidat dvě nová pravidla synchronizace, popsané v následujících částech.


#### <a name="add-an-inbound-sync-rule"></a>Přidání pravidla příchozí synchronizace
Pravidlo příchozí synchronizace znamená, že zdrojem atributu je mezera spojnice a cíl je metaverse. Chcete-li například mít nový tok atributů z místní služby Active Directory do služby Azure Active Directory, vytvořte nové pravidlo příchozí synchronizace. Spusťte **Editor pravidel synchronizace**, **vyberte** příchozí jako směr a vyberte **Přidat nové pravidlo**. 

 ![Editor pravidel synchronizace](media/how-to-connect-fix-default-rules/default3a.png)

Postupujte podle vlastní konvence pojmenování a pojmenujte pravidlo. Zde používáme **Vlastní in z AD - Uživatel**. To znamená, že pravidlo je vlastní pravidlo a je příchozí pravidlo z prostoru konektoru služby Active Directory do metaverse.   

 ![Vytvořit pravidlo příchozí synchronizace](media/how-to-connect-fix-default-rules/default3b.png)

Zadejte svůj vlastní popis pravidla, aby budoucí údržba pravidla byla snadná. Popis může být například založen na tom, jaký je cíl pravidla a proč je potřeba.

Proveďte výběr pro pole **Připojený systém**, **Typ připojeného systémového objektu**a **Typ objektu Metaverse.**

Zadejte hodnotu priority od 0 do 99 (čím nižší číslo, tím vyšší priorita). Pro pole **Značka**, **Povolit synchronizaci hesel**a **Zakázaná** pole použijte výchozí výběry.

Udržujte **filtr oboru** prázdný. To znamená, že pravidlo platí pro všechny objekty spojené mezi připojeným systémem služby Active Directory a metaversem.

Ponechte **pravidla spojení** prázdná. To znamená, že toto pravidlo používá podmínku spojení definovanou ve standardním výchozím pravidle. To je další důvod, proč nezakázat nebo odstranit standardní výchozí pravidlo. Pokud neexistuje žádná podmínka spojení, atribut nebude tok. 

Přidejte odpovídající transformace pro váš atribut. Můžete přiřadit konstantu, aby se konstantní hodnota toku do cílového atributu. Můžete použít přímé mapování mezi zdrojovým nebo cílovým atributem. Nebo můžete použít výraz pro atribut. Zde jsou různé [funkce výrazu,](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-sync-functions-reference) které můžete použít.

#### <a name="add-an-outbound-sync-rule"></a>Přidání pravidla odchozí synchronizace
Chcete-li propojit atribut s cílovým adresářem, je třeba vytvořit odchozí pravidlo. To znamená, že zdroj je metaverse a cíl je připojený systém. Chcete-li vytvořit odchozí pravidlo, spusťte **Editor pravidel synchronizace**, změňte **směr** na **odchozí**a vyberte Přidat **nové pravidlo**. 

![Editor pravidel synchronizace](media/how-to-connect-fix-default-rules/default3c.png)

Stejně jako u příchozího pravidla můžete pravidlo pojmenovat pomocí vlastní konvence pojmenování. Vyberte **připojený systém** jako klienta Azure AD a vyberte připojený systémový objekt, na který chcete nastavit hodnotu atributu. Nastavte prioritu od 0 do 99. 

![Vytvořit pravidlo odchozí synchronizace](media/how-to-connect-fix-default-rules/default3d.png)

Ponechte **filtr oborů** a **pravidla spojení** prázdná. Vyplňte transformaci jako konstantní, přímé nebo výraz. 

Nyní víte, jak vytvořit nový atribut pro tok objektů uživatele ze služby Active Directory do služby Azure Active Directory. Pomocí těchto kroků můžete mapovat libovolný atribut z libovolného objektu na zdroj a cíl. Další informace naleznete v [tématech Vytváření vlastních pravidel synchronizace](how-to-connect-create-custom-sync-rule.md) a [Příprava na zřízení uživatelů](https://docs.microsoft.com/office365/enterprise/prepare-for-directory-synchronization).

### <a name="override-the-value-of-an-existing-attribute"></a>Přepsat hodnotu existujícího atributu
Můžete chtít přepsat hodnotu atributu, který již byl namapován. Například pokud chcete vždy nastavit hodnotu null na atribut ve službě Azure AD, jednoduše vytvořte pouze příchozí pravidlo. Proveďte konstantní `AuthoritativeNull`hodnotu , toku do cílového atributu. 

>[!NOTE] 
> Použijte `AuthoritativeNull` místo `Null` v tomto případě. Důvodem je, že hodnota null nahrazuje hodnotu null, i když má nižší prioritu (vyšší číselná hodnota v pravidle). `AuthoritativeNull`, na druhé straně, není nahrazen a non-null hodnotu jinými pravidly. 

### <a name="dont-sync-existing-attribute"></a>Nesynchronizovat existující atribut
Pokud chcete vyloučit atribut ze synchronizace, použijte funkci filtrování atributů poskytovanou ve službě Azure AD Connect. Spusťte **Azure AD Connect** z ikony na ploše a pak vyberte **Přizpůsobit možnosti synchronizace**.

![Další možnosti úloh Azure AD Connect](media/how-to-connect-fix-default-rules/default4.png)

 Ujistěte se, že je vybrané **filtrování aplikací a atributů Azure AD,** a vyberte **Další**.

![Volitelné funkce Azure AD Connect](media/how-to-connect-fix-default-rules/default5.png)

Zrušte zaškrtnutí atributů, které chcete ze synchronizace vyloučit.

![Atributy Azure AD Connect](media/how-to-connect-fix-default-rules/default6a.png)

## <a name="change-scoping-filter"></a>Změnit filtr oboru
Azure AD Sync se postará o většinu objektů. Můžete zmenšit rozsah objektů a snížit počet objektů, které mají být exportovány, aniž by došlo ke změně standardních výchozích pravidel synchronizace. 

Pomocí jedné z následujících metod zmenšete rozsah objektů, které synchronizujete:

- cloudFiltrovaný atribut
- Filtrování organizačních jednotek

Pokud zmenšíte rozsah synchronizovaných uživatelů, synchronizace hash hesla se také zastaví pro uživatele odfiltrovaného. Pokud se objekty již synchronizují, po zmenšení oboru jsou filtrované objekty odstraněny z cílového adresáře. Z tohoto důvodu se ujistěte, že máte velmi pečlivý rozsah.

>[!IMPORTANT] 
> Zvýšení rozsahu objektů nakonfigurovaných službou Azure AD Connect se nedoporučuje. To ztěžuje týmu podpory společnosti Microsoft pochopit vlastní nastavení. Pokud je nutné zvětšit rozsah objektů, upravte existující pravidlo, klonujte ho a zakažte původní pravidlo. 

### <a name="cloudfiltered-attribute"></a>cloudFiltrovaný atribut
Tento atribut nelze nastavit ve službě Active Directory. Nastavte hodnotu tohoto atributu přidáním nového příchozího pravidla. Potom můžete použít **transformace** a **výraz** nastavit tento atribut v metaverse. Následující příklad ukazuje, že nechcete synchronizovat všechny uživatele, jejichž název oddělení začíná **na HRD** (malá a velká písmena):

`cloudFiltered <= IIF(Left(LCase([department]), 3) = "hrd", True, NULL)`

Nejprve jsme převedli oddělení ze zdroje (Active Directory) na malá písmena. Pak jsme `Left` pomocí funkce vzali pouze první tři znaky `hrd`a porovnali je s . Pokud se shoduje, hodnota je `True`nastavena na , jinak `NULL`. Při nastavování hodnoty na hodnotu null, některé jiné pravidlo s nižší prioritou (vyšší číselná hodnota) může zapisovat do něj s jinou podmínkou. Spusťte náhled na jednom objektu a ověřte pravidlo synchronizace, jak je uvedeno v části [Ověřit pravidlo synchronizace.](#validate-sync-rule)

![Vytvořit možnosti pravidla příchozí synchronizace](media/how-to-connect-fix-default-rules/default7a.png)

### <a name="organizational-unit-filtering"></a>Filtrování organizačních jednotek
Můžete vytvořit jednu nebo více organizačních jednotek (OU) a přesunout objekty, které nechcete synchronizovat s těmito organizačními jednotkami. Potom nakonfigurujte filtrování ou v Azure AD Connect. Spusťte **Azure AD Connect** z ikony na ploše a vyberte následující možnosti. Můžete také nakonfigurovat filtrování ou v době instalace Služby Azure AD Connect. 

![Další úkoly služby Azure AD Connect](media/how-to-connect-fix-default-rules/default8.png)

Postupujte podle průvodce a zrušte zaškrtnutí vyřazuje, které nechcete synchronizovat.

![Možnosti filtrování domény a ou připojení azure a služby AD](media/how-to-connect-fix-default-rules/default9.png)

## <a name="change-join-condition"></a>Změnit podmínku spojení
Použijte výchozí podmínky spojení nakonfigurované službou Azure AD Connect. Změna výchozích podmínek spojení ztěžuje podpoře společnosti Microsoft pochopit vlastní nastavení a podporovat produkt.

## <a name="validate-sync-rule"></a>Ověřit pravidlo synchronizace
Nově přidané pravidlo synchronizace můžete ověřit pomocí funkce náhledu, aniž byste museli spustit úplný cyklus synchronizace. Ve službě Azure AD Connect vyberte **možnost Synchronizační služba**.

![Azure AD Connect se zvýrazněnou synchronizační službou](media/how-to-connect-fix-default-rules/default10.png)

Vyberte **Metaverse Hledání**. Vyberte objekt oboru jako **osobu**, vyberte **Přidat klauzuli**a uveďte kritéria vyhledávání. Dále vyberte **Hledat**a poklepejte na objekt ve výsledcích hledání. Ujistěte se, že vaše data v Azure AD Connect je aktuální pro tento objekt, spuštěním importu a synchronizace v doménové struktuře před spuštěním tohoto kroku.

![Synchronization Service Manager](media/how-to-connect-fix-default-rules/default11.png)

V **části Vlastnosti objektu Metaverse**vyberte **Konektory**, vyberte objekt v odpovídající spojnici (doménové struktuře) a vyberte **Vlastnosti...**.

![Vlastnosti objektu Metaverse](media/how-to-connect-fix-default-rules/default12.png)

Vybrat **náhled...**

![Vlastnosti objektu prostoru spojnice](media/how-to-connect-fix-default-rules/default13a.png)

V okně Náhled vlevém v levém podokně vyberte **Generovat náhled** a **Importovat tok atributů.**

![Preview](media/how-to-connect-fix-default-rules/default14.png)
 
Zde si všimněte, že nově přidané pravidlo je `cloudFiltered` spuštěno na objektu a nastavilatribut na true.

![Preview](media/how-to-connect-fix-default-rules/default15a.png)
 
Chcete-li porovnat upravené pravidlo s výchozím pravidlem, exportujte obě pravidla samostatně jako textové soubory. Tato pravidla se exportují jako soubor skriptu prostředí PowerShell. Můžete je porovnat pomocí libovolného nástroje pro porovnání souborů (například windiff) pro zobrazení změn. 
 
Všimněte si, že `msExchMailboxGuid` v upravené pravidlo atribut se změní na typ **výrazu,** namísto **direct**. Hodnota se také změní na **null** a **ExecuteOnce** možnost. Rozdíly identifikované a priority můžete ignorovat. 

![výstup nástroje windiff](media/how-to-connect-fix-default-rules/default17.png)
 
Chcete-li opravit pravidla a změnit je zpět na výchozí nastavení, odstraňte upravené pravidlo a povolte výchozí pravidlo. Ujistěte se, že neztratíte vlastní nastavení, kterého se snažíte dosáhnout. Až budete připraveni, spusťte **úplnou synchronizaci**.

## <a name="next-steps"></a>Další kroky
- [Hardware a předpoklady](how-to-connect-install-prerequisites.md) 
- [Expresní nastavení](how-to-connect-install-express.md)
- [Vlastní nastavení](how-to-connect-install-custom.md)




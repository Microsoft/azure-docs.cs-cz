---
title: Oprava změněných výchozích pravidel – Azure AD Connect | Microsoft Docs
description: Přečtěte si, jak opravit změněná výchozí pravidla, která jsou součástí Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
editor: curtand
ms.reviewer: darora10
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/21/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd16dd5a20a677568c928f805c1aaa5f2c222f24
ms.sourcegitcommit: c94a177b11a850ab30f406edb233de6923ca742a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/01/2020
ms.locfileid: "89279937"
---
# <a name="fix-modified-default-rules-in-azure-ad-connect"></a>Oprava změněných výchozích pravidel v Azure AD Connect

Služba Azure Active Directory (Azure AD) Connect používá výchozí pravidla pro synchronizaci.  Tato pravidla bohužel nejsou všeobecně použitelná pro všechny organizace. Na základě vašich požadavků je možná budete muset upravit. Tento článek popisuje dva příklady nejběžnějších přizpůsobení a vysvětluje správný způsob, jak tyto úpravy dosáhnout.

>[!NOTE] 
> Úprava stávajících výchozích pravidel pro splnění potřebného přizpůsobení není podporována. Pokud to uděláte, zabrání v budoucích verzích aktualizovat Tato pravidla na nejnovější verzi. Nebudete dostávat opravy chyb, které potřebujete, nebo nové funkce. Tento dokument vysvětluje, jak dosáhnout stejného výsledku, aniž byste museli měnit stávající výchozí pravidla. 

## <a name="how-to-identify-modified-default-rules"></a>Jak identifikovat upravená výchozí pravidla
Počínaje verzí 1.3.7.0 Azure AD Connect je snadné identifikovat upravené výchozí pravidlo. **V nabídce aplikace na ploše**vyberte **Editor pravidel synchronizace**.

![Azure AD Connect s zvýrazněným editorem synchronizačních pravidel](media/how-to-connect-fix-default-rules/default1.png)

V editoru se u všech upravených výchozích pravidel zobrazí před názvem ikona upozornění.

![Ikona upozornění](media/how-to-connect-fix-default-rules/default2.png)

 Zobrazí se také zakázané pravidlo se stejným názvem (Toto je standardní výchozí pravidlo).

![Editor pravidel synchronizace, který zobrazuje standardní výchozí pravidlo a upravené výchozí pravidlo](media/how-to-connect-fix-default-rules/default2a.png)

## <a name="common-customizations"></a>Společná přizpůsobení
Níže jsou uvedené běžné úpravy výchozích pravidel:

- Změnit tok atributů
- Změnit filtr oboru
- Změnit podmínku spojení

Než změníte jakákoli pravidla:

- Zakažte Plánovač synchronizace. Plánovač se ve výchozím nastavení spouští každých 30 minut. Ujistěte se, že se nespouští při provádění změn a odstraňování potíží s novými pravidly. K dočasnému zakázání plánovače spusťte PowerShell a spusťte příkaz `Set-ADSyncScheduler -SyncCycleEnabled $false` .
 ![Příkazy PowerShellu pro zakázání plánovače synchronizace](media/how-to-connect-fix-default-rules/default3.png)

- Změna rozsahu filtru může mít za následek odstranění objektů v cílovém adresáři. Před provedením jakýchkoli změn v oboru objektů buďte opatrní. Doporučujeme, abyste před provedením změn na aktivním serveru provedli změny v přípravném serveru.
- Spusťte náhled na jednom objektu, jak je uvedeno v části [ověření pravidla synchronizace](#validate-sync-rule) po přidání nového pravidla.
- Po přidání nového pravidla nebo úpravou vlastního pravidla synchronizace spusťte úplnou synchronizaci. Tato synchronizace aplikuje nová pravidla na všechny objekty.

## <a name="change-attribute-flow"></a>Změnit tok atributů
Existují tři různé scénáře pro změnu toku atributů:
- Přidání nového atributu.
- Přepsání hodnoty existujícího atributu.
- Volba nesynchronizovat existující atribut.

Můžete to udělat bez změny standardních výchozích pravidel.

### <a name="add-a-new-attribute"></a>Přidat nový atribut
Pokud zjistíte, že atribut neprobíhá z vašeho zdrojového adresáře do cílového adresáře, pomocí [rozšíření Azure AD Connect Sync: Directory](how-to-connect-sync-feature-directory-extensions.md) tento problém opravte.

Pokud pro vás rozšíření nefungují, zkuste přidat dvě nová pravidla synchronizace, která jsou popsaná v následujících částech.


#### <a name="add-an-inbound-sync-rule"></a>Přidat pravidlo příchozí synchronizace
Pravidlo příchozí synchronizace znamená, že zdrojem atributu je místo konektoru a cíl je Metaverse. Pokud třeba chcete pro Azure Active Directory vytvořit nový tok atributů z místní služby Active Directory, vytvořte nové pravidlo příchozí synchronizace. Spusťte **Editor pravidel synchronizace**, vyberte **příchozí** jako směr a vyberte **Přidat nové pravidlo**. 

 ![Editor pravidel synchronizace](media/how-to-connect-fix-default-rules/default3a.png)

Podle vlastního pravidla pojmenování pravidla pojmenujte. Zde používáme **vlastní ve službě AD-User**. To znamená, že pravidlo je vlastní pravidlo a jedná se o příchozí pravidlo z prostoru konektoru služby Active Directory do úložiště metaverse.   

 ![Vytvořit pravidlo příchozí synchronizace](media/how-to-connect-fix-default-rules/default3b.png)

Zadejte vlastní popis pravidla, aby bylo možné budoucí údržbu pravidla snadno. Popis může být například založený na tom, co je cílem pravidla, a proč je potřeba.

Proveďte výběr pro pole **připojeného systému**, **typ připojeného systémového objektu**a **typ objektu úložiště metaverse** .

Zadejte hodnotu priority od 0 do 99 (čím nižší je číslo, tím vyšší je priorita). Pro **značku**, **Povolit synchronizaci hesla**a **zakázaná** pole použijte výchozí výběr.

Nechejte **Filtr oboru** prázdný. To znamená, že pravidlo se vztahuje na všechny objekty, které jsou spojeny mezi systémem připojené služby Active Directory a úložištěm Metaverse.

Nechejte **pravidla pro připojení** prázdná. To znamená, že toto pravidlo používá podmínku spojení definovanou ve standardním výchozím pravidle. Toto je další důvod, proč zakázat nebo odstranit standardní výchozí pravidlo. Pokud neexistuje žádná podmínka spojení, nebude tento atribut tok. 

Přidejte vhodné transformace pro svůj atribut. Můžete přiřadit konstantu pro vytvoření konstantní hodnoty do cílového atributu. Můžete použít přímé mapování mezi zdrojovým nebo cílovým atributem. Nebo můžete použít výraz pro atribut. Tady jsou různé [funkce výrazů](./reference-connect-sync-functions-reference.md) , které můžete použít.

#### <a name="add-an-outbound-sync-rule"></a>Přidat pravidlo odchozí synchronizace
Chcete-li propojit atribut s cílovým adresářem, je třeba vytvořit odchozí pravidlo. To znamená, že zdrojem je úložiště metaverse a cíl je připojený systém. Chcete-li vytvořit odchozí pravidlo, spusťte **Editor pravidel synchronizace**, změňte **směr** na **odchozí**a vyberte **Přidat nové pravidlo**. 

![Editor pravidel synchronizace](media/how-to-connect-fix-default-rules/default3c.png)

Stejně jako u příchozího pravidla můžete pro pojmenování pravidla použít vlastní konvenci vytváření názvů. Vyberte **připojený systém** jako tenant služby Azure AD a vyberte objekt připojeného systému, pro který chcete nastavit hodnotu atributu. Nastavte prioritu od 0 do 99. 

![Vytvořit pravidlo odchozí synchronizace](media/how-to-connect-fix-default-rules/default3d.png)

Nechejte pravidla **filtru oboru** a **spojení** prázdné. Vyplňte transformaci jako konstantu, přímou nebo výraz. 

Nyní se dozvíte, jak vytvořit nový atribut pro tok objektů uživatele ze služby Active Directory do Azure Active Directory. Pomocí těchto kroků lze namapovat libovolný atribut z libovolného objektu na zdroj a cíl. Další informace najdete v tématu [vytváření vlastních pravidel synchronizace](how-to-connect-create-custom-sync-rule.md) a [Příprava na zřízení uživatelů](/office365/enterprise/prepare-for-directory-synchronization).

### <a name="override-the-value-of-an-existing-attribute"></a>Přepsat hodnotu existujícího atributu
Je možné, že budete chtít přepsat hodnotu atributu, který již byl namapován. Pokud třeba chcete nastavit hodnotu null jenom na atribut ve službě Azure AD, stačí jenom vytvořit příchozí pravidlo. Nastavte konstantní hodnotu, `AuthoritativeNull` tok do cílového atributu. 

>[!NOTE] 
> `AuthoritativeNull`Místo `Null` v tomto případě použijte. Důvodem je, že hodnota, která není null, nahradí hodnotu null, i když má nižší prioritu (vyšší číslo hodnoty v pravidle). `AuthoritativeNull`na druhé straně se nenahradí hodnotou jinou než null jinými pravidly. 

### <a name="dont-sync-existing-attribute"></a>Nesynchronizovat existující atribut
Pokud chcete vyloučit atribut z synchronizace, použijte funkci filtrování atributů, která je k dispozici v Azure AD Connect. Z ikony plocha spusťte **Azure AD Connect** a pak vyberte **přizpůsobit možnosti synchronizace**.

![Azure AD Connect možností dalších úloh](media/how-to-connect-fix-default-rules/default4.png)

 Ujistěte se, že je vybraná možnost **filtrování aplikace a atributů Azure AD** , a vyberte **Další**.

![Azure AD Connect volitelné funkce](media/how-to-connect-fix-default-rules/default5.png)

Vymažte atributy, které mají být vyloučeny z synchronizace.

![Atributy Azure AD Connect](media/how-to-connect-fix-default-rules/default6a.png)

## <a name="change-scoping-filter"></a>Změnit filtr oboru
Azure AD Sync postará o většinu objektů. Můžete omezit rozsah objektů a snížit počet objektů, které mají být exportovány, beze změny standardních výchozích pravidel synchronizace. 

K omezení rozsahu objektů, které synchronizujete, použijte jednu z následujících metod:

- cloudFiltered – atribut
- Filtrování organizační jednotky

Pokud omezíte rozsah synchronizovaných uživatelů, u filtrovaných uživatelů se také zastaví synchronizace hodnot hash hesel. Pokud jsou objekty již synchronizovány, po zmenšení rozsahu se odstraněné objekty odstraní z cílového adresáře. Z tohoto důvodu se ujistěte, že je rozsah velmi pečlivě.

>[!IMPORTANT] 
> Zvýšení rozsahu objektů nakonfigurovaných pomocí Azure AD Connect se nedoporučuje. Díky tomu se tým podpory Microsoftu obtížně rozuměle seznámení s vlastními nastaveními. Pokud musíte zvýšit rozsah objektů, upravit stávající pravidlo, klonovat ho a zakázat původní pravidlo. 

### <a name="cloudfiltered-attribute"></a>cloudFiltered – atribut
Tento atribut nemůžete nastavit ve službě Active Directory. Nastavte hodnotu tohoto atributu přidáním nového příchozího pravidla. Pak můžete použít **transformaci** a **výraz** pro nastavení tohoto atributu v úložišti Metaverse. Následující příklad ukazuje, že nechcete synchronizovat všechny uživatele, jejichž název oddělení začíná na **hrd** (bez rozlišení velkých a malých písmen):

`cloudFiltered <= IIF(Left(LCase([department]), 3) = "hrd", True, NULL)`

Nejdříve jsme převedli oddělení ze zdroje (Active Directory) na malá písmena. Potom pomocí `Left` funkce jsme udělali jenom první tři znaky a porovnali je s `hrd` . V případě, že se shoduje, je hodnota nastavena na `True` , jinak `NULL` . V nastavení hodnoty na hodnotu null může jiné pravidlo s nižší prioritou (hodnota vyšší číslo) zapisovat do ní s jinou podmínkou. Spusťte náhled na jednom objektu pro ověření pravidla synchronizace, jak je uvedeno v části [ověření pravidla synchronizace](#validate-sync-rule) .

![Možnosti vytvoření pravidla synchronizace příchozích synchronizací](media/how-to-connect-fix-default-rules/default7a.png)

### <a name="organizational-unit-filtering"></a>Filtrování organizační jednotky
Můžete vytvořit jednu nebo více organizačních jednotek (OU) a přesunout objekty, které nechcete synchronizovat s těmito organizačními jednotkami. Pak nakonfigurujte filtrování organizačních jednotek v Azure AD Connect. Spusťte **Azure AD Connect** z ikony desktopu a vyberte následující možnosti. Můžete také nakonfigurovat filtrování organizačních jednotek v době instalace Azure AD Connect. 

![Azure AD Connect další úkoly](media/how-to-connect-fix-default-rules/default8.png)

Postupujte podle pokynů průvodce a vymažte jednotky, které nechcete synchronizovat.

![Azure AD Connect možností filtrování domén a organizačních jednotek](media/how-to-connect-fix-default-rules/default9.png)

## <a name="change-join-condition"></a>Změnit podmínku spojení
Použijte výchozí podmínky spojení nakonfigurované pomocí Azure AD Connect. Změna výchozích podmínek pro připojení ztěžuje podpoře Microsoftu pochopení přizpůsobení a podpory produktu.

## <a name="validate-sync-rule"></a>Ověřit pravidlo synchronizace
Nově přidané pravidlo synchronizace můžete ověřit pomocí funkce Preview, aniž byste museli spustit úplný cyklus synchronizace. V Azure AD Connect vyberte **synchronizační služba**.

![Azure AD Connect se zvýrazněnou synchronizační službou](media/how-to-connect-fix-default-rules/default10.png)

Vyberte **vyhledávání v úložišti Metaverse**. Vyberte objekt oboru jako **osoba**, vyberte **Přidat klauzuli**a uveďte kritéria hledání. V dalším kroku vyberte **Hledat**a dvakrát klikněte na objekt ve výsledcích hledání. Ujistěte se, že jsou vaše data v Azure AD Connect pro daný objekt aktuální, spuštěním importu a synchronizace v doménové struktuře před spuštěním tohoto kroku.

![Synchronization Service Manager](media/how-to-connect-fix-default-rules/default11.png)

V části **vlastnosti objektu úložiště metaverse**vyberte **konektory**, vyberte objekt v odpovídajícím konektoru (doménové struktuře) a vyberte **Vlastnosti...**.

![Vlastnosti objektu úložiště metaverse](media/how-to-connect-fix-default-rules/default12.png)

Vybrat **Náhled...**

![Vlastnosti objektu prostoru konektoru](media/how-to-connect-fix-default-rules/default13a.png)

V okně Náhled vyberte v levém podokně **vygenerovat náhled** a **Importovat tok atributů** .

![Preview](media/how-to-connect-fix-default-rules/default14.png)
 
Zde si všimněte, že nově přidané pravidlo je spuštěno na objektu a má nastaven `cloudFiltered` atribut na hodnotu true.

![Preview](media/how-to-connect-fix-default-rules/default15a.png)
 
Chcete-li porovnat upravené pravidlo s výchozím pravidlem, exportujte obě pravidla samostatně jako textové soubory. Tato pravidla se exportují jako soubor skriptu PowerShellu. Můžete je porovnat pomocí jakéhokoli nástroje pro porovnání souborů (například Windiff), aby se změny zobrazily. 
 
Všimněte si, že ve změněném pravidle `msExchMailboxGuid` je atribut změněn na typ **výrazu** místo **přímého**. Hodnota je také změněna na **hodnotu null** a možnost **ExecuteOnce** . Můžete ignorovat rozdíly v identifikovaných a prioritách. 

![výstup nástroje Windiff](media/how-to-connect-fix-default-rules/default17.png)
 
Pokud chcete pravidla změnit tak, aby se změnila zpátky na výchozí nastavení, odstraňte upravené pravidlo a povolte výchozí pravidlo. Ujistěte se, že neztratíte vlastní nastavení, které se snažíte dosáhnout. Až budete připraveni, spusťte **úplnou synchronizaci**.

## <a name="next-steps"></a>Další kroky
- [Hardware a předpoklady](how-to-connect-install-prerequisites.md) 
- [Expresní nastavení](how-to-connect-install-express.md)
- [Vlastní nastavení](how-to-connect-install-custom.md)
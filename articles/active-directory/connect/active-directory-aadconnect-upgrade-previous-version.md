---
title: 'Azure AD Connect: Upgrade z předchozí verze | Microsoft Docs'
description: Vysvětluje různé metody pro upgrade na nejnovější verzi Azure Active Directory Connect, včetně místní upgrade a migrace dráha.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 31f084d8-2b89-478c-9079-76cf92e6618f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Identity
ms.date: 07/12/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 1a6fe4fc7fd5f47bfd4bc4d9168f76c31c78b47b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34592472"
---
# <a name="azure-ad-connect-upgrade-from-a-previous-version-to-the-latest"></a>Azure AD Connect: Upgrade z předchozí verze na nejnovější
Toto téma popisuje různé metody, které můžete použít k upgradu vaší instalace služby Azure Active Directory (Azure AD) Connect na nejnovější verzi. Doporučujeme ponechat si sami aktuální verze služby Azure AD Connect. Můžete také použít kroky v [kyvu migrace](#swing-migration) části při provedení podstatných změn v konfiguraci.

Pokud chcete provést upgrade z nástroje DirSync, přečtěte si téma [Upgrade ze synchronizačního nástroje služby Azure AD (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md) místo.

Existuje několik různých strategií, které můžete použít k upgradu Azure AD Connect.

| Metoda | Popis |
| --- | --- |
| [Automatický upgrade](active-directory-aadconnect-feature-automatic-upgrade.md) |Toto je nejsnazší pro zákazníky s rychlé instalace. |
| [Místní upgrade](#in-place-upgrade) |Pokud máte jeden server, můžete upgradovat instalaci na místě na stejném serveru. |
| [Dráha migrace](#swing-migration) |Dva servery můžete připravit jeden ze serverů se nová verze nebo konfigurace a změnit aktivního serveru, až budete připraveni. |

Oprávnění informace najdete v tématu [oprávnění vyžadovaná pro upgrade](active-directory-aadconnect-accounts-permissions.md#upgrade).

> [!NOTE]
> Poté, co jste povolili nový server Azure AD Connect zahájit synchronizaci změny do Azure AD, můžete nesmí vrátit zpět k použití nástroje DirSync nebo Azure AD Sync. Přechod na starší verzi z Azure AD Connect pro starší klienty, včetně nástroje DirSync a Azure AD Sync, není podporována a může vést k problémům, jako je například ztráty dat ve službě Azure AD.

## <a name="in-place-upgrade"></a>Místní upgrade
Místní upgrade funguje pro přechod z Azure AD Sync nebo Azure AD Connect. Nefunguje pro přesun z nástroje DirSync nebo řešení s Forefront Identity Manager (FIM) + konektoru služby Azure AD.

Tato metoda je upřednostňovaný, pokud máte jeden server a o méně než 100 000 objektů. Pokud se nějak změní out-of-box synchronizační pravidla, úplný import a úplnou synchronizaci vzniknout po upgradu. Tato metoda zajišťuje, že se nová konfigurace se použije pro všechny existující objekty v systému. To běžet může trvat několik hodin v závislosti na počet objektů, které jsou v oboru synchronizační modul. Plánovač normální rozdílová synchronizace (který ve výchozím nastavení synchronizuje každých 30 minut) je pozastavená, ale pokračuje v synchronizaci hesel. Můžete uvažovat o provedením upgradu na místě během víkendu. Pokud se, že neexistují že žádné změny konfigurace out-of-box službou Azure AD Connect novou verzi, pak normální rozdílová importu nebo synchronizace spustí místo.  
![Místní upgrade](./media/active-directory-aadconnect-upgrade-previous-version/inplaceupgrade.png)

Pokud jste udělali změny out-of-box synchronizační pravidla, tato pravidla jsou nastavit zpět na výchozí konfiguraci při upgradu. Abyste měli jistotu, že je mezi upgrady udržována konfiguraci, ujistěte se, provést změny, jak se popisuje v [osvědčené postupy pro změnu výchozí konfigurace](active-directory-aadconnectsync-best-practices-changing-default-configuration.md).

Během upgradu na místě, může být změny zavedl, které vyžadují určité synchronizace aktivity (včetně kroky úplný Import a úplnou synchronizaci) má být proveden po dokončení upgradu. Odložení takových aktivit, informace naleznete v sekci [postup odložení úplnou synchronizaci po upgradu](#how-to-defer-full-synchronization-after-upgrade).

Pokud používáte Azure AD Connect s nestandardní konektor (například obecné konektor LDAP a obecné konektor SQL), je nutné aktualizovat odpovídající konfigurace konektoru v [Synchronization Service Manager](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-service-manager-ui-connectors) Po upgradu na místě. Podrobnosti o tom, jak aktualizovat konfiguraci konektoru naleznete v části [historie verzí konektoru – řešení potíží s](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-connector-version-history#troubleshooting). Pokud není aktualizace konfigurace, import a export spustit kroky nebude fungovat správně pro konektor. Zobrazí se následující chyby v protokolu událostí aplikace se zprávou *"verze sestavení v konfiguraci konektoru AAD ("X.X.XXX. X"), je dřívější než ve skutečnosti verze ("X.X.XXX. X") z"C:\Program Files\Microsoft Azure AD Sync\Extensions\Microsoft.IAM.Connector.GenericLdap.dll".*

## <a name="swing-migration"></a>Postupná migrace
Pokud máte komplexní nasazení nebo mnoho objektů, může to být nepraktické provést místní upgrade systému za provozu. Pro některé zákazníky tento proces může trvat několik dní – a během této doby se zpracují žádné rozdílové změny. Tato metoda slouží také když máte v plánu provést konfiguraci významné změny a chcete si je vyzkoušet předtím, než se instaluje do cloudu.

Doporučené metody pro tyto scénáře se má používat dráha migrace. Je nutné (aspoň) dva servery – jednu aktivní a jeden pracovní server. Aktivní server (ukázka s plnou modré čáry na následujícím obrázku) zodpovídá za active produkční zatížení. Pracovní server (ukázka s přerušované čáry fialové) je připravený pomocí nové verze nebo konfigurace. Pokud je plně připraven, se provádí tento server aktivní. Předchozí aktivního serveru, která teď má předchozí verzi nebo nainstalovaná konfigurace, se provádí na testovacím serveru a upgradu.

Dva servery můžete použít různé verze. Například active server, který chcete vyřadit z provozu můžete použít Azure AD Sync, a nový pracovní server můžete použít Azure AD Connect. Pokud používáte migraci dráha vyvíjet novou konfiguraci, je vhodné tak, aby měl shodovat s verzemi na dvou serverech.  
![Pracovní server](./media/active-directory-aadconnect-upgrade-previous-version/stagingserver1.png)

> [!NOTE]
> Někteří zákazníci dávají přednost mít tři nebo čtyři servery pro tento scénář. Při upgradu serveru pracovní nemáte záložní server pro [zotavení po havárii](active-directory-aadconnectsync-operations.md#disaster-recovery). Se tři nebo čtyři servery můžete připravit jednu sadu serverů primární nebo úsporného režimu s novou verzí, které zajišťuje, že existuje vždy pracovní server, který je připravena převzít roli.

Tyto kroky také fungovat pro přesun z Azure AD Sync nebo řešení s FIM + konektoru služby Azure AD. Tyto kroky nefungují pro nástroj DirSync, ale je stejnou dráha migrace metodu (také nazývané paralelní nasazení) s kroky pro nástroj DirSync v [Upgrade Azure Active Directory sync (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md).

### <a name="use-a-swing-migration-to-upgrade"></a>Použijte dráha migraci k upgradu
1. Pokud používáte Azure AD Connect na obou serverech a plánujete provést pouze konfiguraci změnit, ujistěte se, že aktivní server a pracovní server používáte stejnou verzi. Který usnadňuje porovnat rozdíly později. Pokud provádíte upgrade ze služby Azure AD Sync, tyto servery mají různé verze. Pokud provádíte upgrade ze starší verze služby Azure AD Connect, je vhodné spustit s dvěma servery, které používají stejnou verzi, ale není to nutné.
2. Pokud jste udělali vlastní konfiguraci a pracovní server nemá, postupujte podle kroků v části [přesunout vlastní konfiguraci z aktivního serveru k serveru pracovní](#move-custom-configuration-from-active-to-staging-server).
3. Pokud provádíte upgrade ze starší verze služby Azure AD Connect, upgradujte na nejnovější verzi pracovní server. Při přesouvání z Azure AD Sync, nainstalujte Azure AD Connect na testovacím serveru.
4. Umožní synchronizační modul spustit úplný import a úplnou synchronizaci na testovacím serveru.
5. Ověřte, že nová konfigurace nebyla způsobit neočekávané změny pomocí kroků v části "Ověřit" v [ověřte konfiguraci serveru](active-directory-aadconnectsync-operations.md#verify-the-configuration-of-a-server). Pokud není něco podle očekávání, opravte ji, spusťte import a synchronizaci a ověřit data, dokud ho spokojeni, pomocí následujících kroků.
6. Přepínač pracovní serveru, aby se aktivní server. Toto je poslední krok "Přepínač aktivního serveru" v [ověřte konfiguraci serveru](active-directory-aadconnectsync-operations.md#verify-the-configuration-of-a-server).
7. Pokud upgradujete Azure AD Connect, upgradujte server, který je nyní v pracovním režimu na nejnovější verzi. Postupujte stejným způsobem jako před dat a konfigurace upgradovat. Pokud jste upgradovali z Azure AD Sync, teď můžete vypnout a vyřadit z provozu starý server.

### <a name="move-a-custom-configuration-from-the-active-server-to-the-staging-server"></a>Přesunout vlastní konfiguraci z aktivního serveru pracovní server
Pokud jste provedli změny konfigurace aktivního serveru, budete muset Ujistěte se, že stejné změny se použijí k serveru pracovní. Tento přesun usnadní, můžete použít [nástroje Azure AD Connect konfigurace dokumentace](https://github.com/Microsoft/AADConnectConfigDocumenter).

Můžete přesunout vlastní synchronizační pravidla, že jste vytvořili pomocí prostředí PowerShell. Musíte použít jiné změny stejným způsobem jako ve všech systémech, a nemůžete migrovat, změny. [Konfigurace dokumentace](https://github.com/Microsoft/AADConnectConfigDocumenter) vám může pomoct porovnání těchto dvou systémech, abyste měli jistotu, že jsou identické. Tento nástroj může také pomoci při automatizaci kroky uvedenými v této části.

Musíte nakonfigurovat stejným způsobem jako následujících akcí na obou serverech:

* Připojení ke stejné doménové struktury
* Všechny domény a filtrování organizační jednotky
* Stejné volitelné funkce, jako je například synchronizace hesel a zpětný zápis hesla

**Přesunout vlastní synchronizační pravidla**  
Chcete-li přesunout vlastní synchronizační pravidla, postupujte takto:

1. Otevřete **editoru pravidel synchronizace** na váš aktivní server.
2. Vyberte vlastní pravidlo. Klikněte na tlačítko **exportovat**. Otevře okno Poznámkový blok. Uložte dočasný soubor s příponou PS1. Díky tomu skript prostředí PowerShell. Zkopírujte soubor PS1 pracovní server.  
   ![Export pravidla synchronizace](./media/active-directory-aadconnect-upgrade-previous-version/exportrule.png)
3. Identifikátor GUID konektoru se liší na testovacím serveru a musíte ji změnit. Chcete-li získat identifikátor GUID, spusťte **editoru pravidel synchronizace**, vyberte jednu z out-of-box pravidla, která představují stejnou připojený systém a klikněte na tlačítko **exportovat**. Identifikátor GUID v souboru PS1 nahraďte identifikátor GUID z pracovního serveru.
4. V příkazovém řádku prostředí PowerShell spusťte soubor PS1. Tím se vytvoří vlastní synchronizačního pravidla na testovacím serveru.
5. Tento postup opakujte pro všechny vaše vlastní pravidla.

## <a name="how-to-defer-full-synchronization-after-upgrade"></a>Postup odložení úplnou synchronizaci po upgradu
Během upgradu na místě, může být změny zavedl, které vyžadují konkrétní synchronizace aktivity (včetně kroky úplný Import a úplnou synchronizaci) má být proveden. Například vyžadovat změny schématu konektor **úplný import** krok a out-of-box změny pravidlo synchronizace vyžadují **úplné synchronizace** krok provést na ovlivněných konektory. Během upgradu, Azure AD Connect určí, jaké aktivity synchronizace se vyžadují a zaznamenává je jako *přepsání*. V následující synchronizační cyklus plánovače synchronizace převezme tato přepsání a spustí je. Jakmile přepsání úspěšně proveden, je odebrána.

Mohou nastat situace, kdy nechcete, aby tato přepsání proběhla okamžitě po provedení upgradu. Například máte mnoho synchronizované objekty a chcete tyto kroky synchronizace dochází po pracovní dobu. Chcete-li odebrat tato přepsání:

1. Během upgradu **zrušte zaškrtnutí políčka** možnost **po dokončení konfigurace spustit proces synchronizace**. To zakáže plánovače synchronizace a zabrání synchronizační cyklus probíhají automaticky předtím, než se odeberou přepsání.

   ![DisableFullSyncAfterUpgrade](./media/active-directory-aadconnect-upgrade-previous-version/disablefullsync01.png)

2. Po dokončení upgradu, spusťte následující rutinu a zjistěte, jaké přepsání byly přidány: `Get-ADSyncSchedulerConnectorOverride | fl`

   >[!NOTE]
   > Přepsání jsou specifické pro konektor. V následujícím příkladu kroky úplný Import a úplnou synchronizaci jsou přidané do obou místní konektor AD a Azure AD Connector.

   ![DisableFullSyncAfterUpgrade](./media/active-directory-aadconnect-upgrade-previous-version/disablefullsync02.png)

3. Poznamenejte si stávající přepsání, které byly přidány.
   
4. Chcete-li odebrat přepsání pro úplný import a úplnou synchronizaci v libovolné konektoru, spusťte následující rutinu: `Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid-of-ConnectorIdentifier> -FullImportRequired $false -FullSyncRequired $false`

   Chcete-li odebrat přepsání na všechny konektory, spusťte následující skript prostředí PowerShell:

   ```
   foreach ($connectorOverride in Get-ADSyncSchedulerConnectorOverride)
   {
       Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier $connectorOverride.ConnectorIdentifier.Guid -FullSyncRequired $false -FullImportRequired $false
   }
   ```

5. Pokud chcete obnovit Plánovač, spusťte následující rutinu: `Set-ADSyncScheduler -SyncCycleEnabled $true`

   >[!IMPORTANT]
   > Musíte provést kroky požadované synchronizace v nejdřívější usnadnění práce. Můžete buď ručně provést tyto kroky, pomocí Synchronization Service Manager nebo přidat přepsání zpět pomocí rutiny Set-ADSyncSchedulerConnectorOverride.

Přepsání pro úplný import a úplnou synchronizaci v libovolné konektoru, spusťte následující rutinu:  `Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid> -FullImportRequired $true -FullSyncRequired $true`

## <a name="next-steps"></a>Další postup
Další informace o [integrace místních identit s Azure Active Directory](active-directory-aadconnect.md).

---
title: 'Azure AD Connect: Upgrade z předchozí verze | Dokumentace Microsoftu'
description: Vysvětluje různé metody pro upgrade na nejnovější verzi Azure Active Directory Connect, včetně místního upgradu a postupné migraci.
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
ms.date: 07/18/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: b730f80faa031b1866d3c11d8a2c885ec67f965e
ms.sourcegitcommit: a1140e6b839ad79e454186ee95b01376233a1d1f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2018
ms.locfileid: "43144316"
---
# <a name="azure-ad-connect-upgrade-from-a-previous-version-to-the-latest"></a>Azure AD Connect: Upgrade z předchozí verze na nejnovější verzi
Toto téma popisuje různé metody, které můžete použít k upgradu na nejnovější verzi vaší instalace služby Azure Active Directory (Azure AD) Connect. Doporučujeme ponechat si aktuální s verzemi služby Azure AD Connect. Také postupujte podle kroků v [Postupná migrace](#swing-migration) části Pokud provedete významné změny konfigurace.

Pokud chcete provést upgrade z nástroje DirSync, přečtěte si téma [Upgrade ze synchronizačního nástroje služby Azure AD (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md) místo.

Existuje několik různé strategie, které můžete použít k upgradu služby Azure AD Connect.

| Metoda | Popis |
| --- | --- |
| [Automatický upgrade](active-directory-aadconnect-feature-automatic-upgrade.md) |Toto je nejjednodušší metoda pro zákazníky s Expresní instalace. |
| [Místní upgrade](#in-place-upgrade) |Pokud máte jeden server, můžete upgradovat instalaci na místě na stejném serveru. |
| [Postupná migrace](#swing-migration) |Dva servery můžete připravit jeden ze serverů se nová verze nebo konfigurace a změnit server, active, až budete připravení. |

Informace o oprávnění, najdete v článku [oprávnění potřebná pro upgrade](active-directory-aadconnect-accounts-permissions.md#upgrade).

> [!NOTE]
> Po povolení nový server Azure AD Connect zahájení synchronizace změn do služby Azure AD, nemůžete se už vrátit k používání DirSync nebo Azure AD Sync. Downgrade z Azure AD Connect na starší verze klientů, včetně nástrojů DirSync a Azure AD Sync, není podporováno a může vést k problémům, například ke ztrátě dat ve službě Azure AD.

## <a name="in-place-upgrade"></a>Místní upgrade
Místní upgrade funguje pro přechod z Azure AD Sync nebo Azure AD Connect. Nefunguje pro přesun z nástroje DirSync nebo řešení Forefront Identity Manager (FIM) + konektoru služby Azure AD.

Tato metoda se upřednostňuje v případě, že máte jeden server a o méně než 100 000 objektů. Pokud jsou všechny změny out-of-box synchronizační pravidla, úplný import a úplnou synchronizaci vzniknout po upgradu. Tato metoda zajišťuje, že nová konfigurace se použije u všech existujících objektů v systému. Tento běh může trvat několik hodin v závislosti na počtu objektů, které jsou v rozsahu synchronizačního modulu. Plánovač synchronizační normální rozdílová (což ve výchozím nastavení synchronizuje každých 30 minut) je pozastavená, ale pokračuje v synchronizaci hesel. Můžete zvážit provedení místního upgradu během víkendu. Pokud nejsou žádné změny konfigurace out-of-box s Azure AD Connect novou verzi, pak normální rozdílová import nebo synchronizaci spustí místo.  
![Místní upgrade](./media/active-directory-aadconnect-upgrade-previous-version/inplaceupgrade.png)

Pokud jste provedli změny out-of-box synchronizační pravidla, tato pravidla jsou nastaveny zpět na výchozí nastavení při upgradu. Pokud chcete mít jistotu, že se ukládají vaše konfigurace mezi upgrady, ujistěte se, že provedete změny, jak jste je popsáno v [osvědčené postupy pro změnu výchozí konfigurace](active-directory-aadconnectsync-best-practices-changing-default-configuration.md).

Při upgradu na místě, může být změny zavedené, které vyžadují konkrétní synchronizační aktivity (včetně kroky úplný Import a úplnou synchronizaci), který se spustí po dokončení upgradu. Odložení takovýchto aktivit, najdete v části [jak odložit úplnou synchronizaci po upgradu](#how-to-defer-full-synchronization-after-upgrade).

Pokud používáte Azure AD Connect s nestandardní konektor (například obecný konektor LDAP a pro obecný konektor SQL), musíte aktualizovat odpovídající konfigurace konektoru v [Synchronization Service Manager](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-service-manager-ui-connectors) Po upgradu na místě. Podrobnosti o tom, jak aktualizovat konfiguraci konektoru, najdete v části [historie verzí konektoru - řešení potíží s](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-connector-version-history#troubleshooting). Pokud není aktualizace konfigurace, import a export spustit kroky nebude fungovat správně při použití konektoru. Zobrazí se následující chybu v protokolu událostí aplikace se zprávou *"verze sestavení v konfiguraci konektoru AAD ("X.X.XXX. X") je dřívější než aktuální verze ("X.X.XXX. X") z"C:\Program Files\Microsoft Azure AD Sync\Extensions\Microsoft.IAM.Connector.GenericLdap.dll".*

## <a name="swing-migration"></a>Postupná migrace
Pokud máte velký počet objektů nebo komplexních nasazení, může být nepraktické provést místní upgrade na systém za provozu. Pro některé zákazníky tento proces může trvat několik dní – a během této doby jsou zpracovány žádné rozdílové změny. Tuto metodu můžete použít také pokud plánujete udělat podstatné změny konfigurace a chcete si je vyzkoušet předtím, než jste nahráli do cloudu.

Doporučenou metodou pro tyto scénáře je použití postupnou migraci. Potřebujete (minimálně) dva servery – jeden aktivní server a jedna pracovní server. Aktivní server (zobrazeno se modré čáry na následujícím obrázku) zodpovídá za aktivní produkční zatížení. Pracovní server (ukázka s přerušované čáry fialová) je připravený pomocí nové vydané verze nebo konfigurace. Pokud je to plně připravené, tento server se stane aktivní. Předchozí active server, která nyní obsahuje starší verze nebo konfigurace nainstalované, se provádí na testovacím serveru a se upgraduje.

Dva servery můžete použít různé verze. Například aktivní server, který chcete vyřadit z provozu, můžete použít Azure AD Sync, a nové pracovní server můžete použít Azure AD Connect. Pokud pomocí migrace swing vyvíjet novou konfiguraci, je dobré mít stejné verze na dvou serverech.  
![Pracovní server](./media/active-directory-aadconnect-upgrade-previous-version/stagingserver1.png)

> [!NOTE]
> Zákazníci, kteří mají tři nebo čtyři servery pro tento scénář dávají přednost. Pokud se upgraduje pracovní server není nutné záložní server pro [zotavení po havárii](active-directory-aadconnectsync-operations.md#disaster-recovery). Tři nebo čtyři servery můžete připravit jednu sadu serverů primární/úsporný režim s novou verzi, která zajistí, že je vždycky pracovní server, který je připraven k převzetí.

Tyto kroky pro přesun z Azure AD Sync nebo řešení s FIM + konektoru služby Azure AD také pracovat. Tyto kroky nefungují pro nástroj DirSync, ale stejné postupné migrace (neboli paralelní nasazení) s kroky pro nástroj DirSync je metoda v [Upgrade služby Azure Active Directory sync (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md).

### <a name="use-a-swing-migration-to-upgrade"></a>Pomocí migrace swing upgradovat
1. Pokud používáte Azure AD Connect na obou serverech a v plánu provést pouze konfiguraci změnit, ujistěte se, že váš aktivní server a pracovní server jsou obě používají stejnou verzi. Který usnadňuje později porovnat rozdíly. Pokud provádíte upgrade ze služby Azure AD Sync, tyto servery mají různé verze. Pokud upgradujete ze starší verze služby Azure AD Connect, je vhodné spustit se dvěma servery, které používají stejnou verzi, ale to není nutné.
2. Pokud jste provedli vlastní konfigurace a pracovní server nemá, postupujte podle kroků v části [přesunout vlastní konfiguraci z aktivního serveru pracovní server](#move-a-custom-configuration-from-the-active-server-to-the-staging-server).
3. Pokud upgradujete ze starší verze služby Azure AD Connect, upgradujte pracovní server na nejnovější verzi. Pokud přesouváte ze služby Azure AD Sync, nainstalujte na testovacím serveru služby Azure AD Connect.
4. Umožní synchronizační modul na testovacím serveru spustit úplný import a úplnou synchronizaci.
5. Ověřte, že novou konfiguraci neměli způsobit neočekávané změny pomocí kroků v části "Ověřit" v [ověřte konfiguraci serveru](active-directory-aadconnectsync-operations.md#verify-the-configuration-of-a-server). Pokud není něco podle očekávání, opravte ji, spusťte import a synchronizaci a ověřit data, dokud vypadá dobře, pomocí následujících kroků.
6. Přepněte pracovní server byl aktivní server. Toto je poslední krok "Přepínač aktivního serveru" v [ověřte konfiguraci serveru](active-directory-aadconnectsync-operations.md#verify-the-configuration-of-a-server).
7. Pokud provádíte upgrade služby Azure AD Connect, upgradujte server, který je teď v pracovním režimu na nejnovější verzi. Postupujte stejným způsobem jako před k získání dat a konfigurace upgradu. Pokud jste provedli upgrade ze služby Azure AD Sync, můžete nyní vypnout a váš starý server vyřadit z provozu.

### <a name="move-a-custom-configuration-from-the-active-server-to-the-staging-server"></a>Přesunout vlastní konfigurace z aktivního serveru na testovacím serveru
Pokud jste provedli změny v konfiguraci aktivní server, budete muset Ujistěte se, že stejné změny se použijí pracovní server. Smyslem tohoto přesunutí, můžete použít [dokumentace služby Azure AD Connect konfigurace](https://github.com/Microsoft/AADConnectConfigDocumenter).

Můžete přesunout že vlastní synchronizační pravidla, že jste vytvořili pomocí prostředí PowerShell. Musíte použít jiné změny stejným způsobem jako ve všech systémech a není možné migrovat změny. [Konfigurace dokumentace](https://github.com/Microsoft/AADConnectConfigDocumenter) vám umožňují porovnání těchto dvou systémech, abyste měli jistotu, že jsou identické. Nástroj může také pomoct při automatizaci kroky v této části.

Budete muset nakonfigurovat následující věci stejným způsobem jako na obou serverech:

* Připojení ke stejné doménové struktury
* Všechny domény a filtrování podle organizačních jednotek
* Stejné volitelné funkce, jako je například synchronizace hesel a zpětný zápis hesla

**Přesunout vlastní synchronizační pravidla**  
Pokud chcete přesunout vlastní synchronizační pravidla, postupujte takto:

1. Otevřít **Editor pravidel synchronizace** na váš aktivní server.
2. Vyberte vlastní pravidlo. Klikněte na tlačítko **exportovat**. Otevře se okno Poznámkový blok. Dočasný soubor uložte s příponou PS1. Díky tomu skript prostředí PowerShell. Zkopírujte soubor PS1 pracovní server.  
   ![Export pravidla synchronizace](./media/active-directory-aadconnect-upgrade-previous-version/exportrule.png)
3. Identifikátor GUID konektoru se liší na testovacím serveru a je nutné změnit. Chcete-li získat identifikátor GUID, spusťte **Editor pravidel synchronizace**, vyberte jednu z out-of-box pravidla, která představují stejné připojený systém a klikněte na tlačítko **exportovat**. Identifikátor GUID v souboru PS1 nahraďte identifikátorem GUID z pracovního serveru.
4. V řádku prostředí PowerShell spusťte soubor PS1. Tím se vytvoří vlastní synchronizačního pravidla na testovacím serveru.
5. Tento postup opakujte pro všechna vlastní pravidla.

## <a name="how-to-defer-full-synchronization-after-upgrade"></a>Jak odložit úplnou synchronizaci po upgradu
Při upgradu na místě, může být změny zavedené, které vyžadují konkrétní synchronizační aktivity (včetně kroky úplný Import a úplnou synchronizaci) má být proveden. Například vyžadují změny schématu konektor **úplný import** krok a out-of-box změny pravidel synchronizace vyžadují **úplné synchronizace** krok má být proveden na ovlivněných konektory. Během upgradu, Azure AD Connect, určuje, jaké aktivity synchronizace jsou povinné a zaznamenává je jako *přepíše*. V následující synchronizační cyklus Plánovač synchronizace vybere tato přepsání a provede je. Jakmile přepsání je úspěšně spuštěn, bude odebrán.

Může nastat situace, kdy nechcete, aby tato přepsání projevily hned po upgradu. Například máte mnoho synchronizované objekty a chcete tyto kroky synchronizace má být provedena po pracovní době. Chcete-li odebrat tato přepsání:

1. Během upgradu **zrušte zaškrtnutí políčka** možnost **po dokončení konfigurace spustit proces synchronizace**. To zakáže Plánovač synchronizace a zabrání synchronizační cyklus dochází automaticky před odstraněním přepsání.

   ![DisableFullSyncAfterUpgrade](./media/active-directory-aadconnect-upgrade-previous-version/disablefullsync01.png)

2. Po dokončení upgradu, zjistěte, jaké přepsání byly přidány následující rutiny: `Get-ADSyncSchedulerConnectorOverride | fl`

   >[!NOTE]
   > Přepsání jsou specifické pro konektor. V následujícím příkladu kroky úplný Import a úplnou synchronizaci byly přidány do obou místní AD Connector a konektor služby Azure AD.

   ![DisableFullSyncAfterUpgrade](./media/active-directory-aadconnect-upgrade-previous-version/disablefullsync02.png)

3. Poznamenejte si stávající přepsání, které byly přidány.
   
4. Odebrat přepisy pro úplný import a úplnou synchronizaci konektoru služby libovolného, spusťte následující rutinu: `Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid-of-ConnectorIdentifier> -FullImportRequired $false -FullSyncRequired $false`

   Chcete-li odebrat přepsání na všechny konektory, spusťte následující skript prostředí PowerShell:

   ```
   foreach ($connectorOverride in Get-ADSyncSchedulerConnectorOverride)
   {
       Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier $connectorOverride.ConnectorIdentifier.Guid -FullSyncRequired $false -FullImportRequired $false
   }
   ```

5. Pokud chcete obnovit Plánovač, spusťte následující rutinu: `Set-ADSyncScheduler -SyncCycleEnabled $true`

   >[!IMPORTANT]
   > Nezapomeňte, proveďte nejdříve kroky požadované synchronizace. Můžete buď ručně provést tento postup pomocí Synchronization Service Manager nebo přidat přepsání zpátky pomocí rutiny Set-ADSyncSchedulerConnectorOverride.

Přidání přepsání pro úplný import a úplnou synchronizaci v libovolné konektoru, spusťte následující rutinu:  `Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid> -FullImportRequired $true -FullSyncRequired $true`

## <a name="troubleshooting"></a>Řešení potíží
Následující část obsahuje řešení problémů a informace, které můžete použít, pokud narazíte na problém, upgrade služby Azure AD Connect.

### <a name="azure-active-directory-connector-missing-error-during-azure-ad-connect-upgrade"></a>Azure Active Directory connector chybějící při došlo k chybě služby Azure AD Connect upgradovat

Když upgradujete z předchozí verze služby Azure AD Connect, budete pravděpodobně dojde k chybě na začátku upgradu 

![Chyba](./media/active-directory-aadconnect-upgrade-previous-version/error1.png)

K této chybě dochází, protože konektor služby Azure Active Directory s identifikátorem b891884f-051e-4a83-95af - 2544101c 9083, neexistuje v aktuální konfiguraci Azure AD Connect. Pokud chcete ověřit, že jde o tento případ, otevřete okno prostředí PowerShell, spusťte rutinu `Get-ADSyncConnector -Identifier b891884f-051e-4a83-95af-2544101c9083`

```
PS C:\> Get-ADSyncConnector -Identifier b891884f-051e-4a83-95af-2544101c9083
Get-ADSyncConnector : Operation failed because the specified MA could not be found.
At line:1 char:1
+ Get-ADSyncConnector -Identifier b891884f-051e-4a83-95af-2544101c9083
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : ReadError: (Microsoft.Ident...ConnectorCmdlet:GetADSyncConnectorCmdlet) [Get-ADSyncConne
   ctor], ConnectorNotFoundException
    + FullyQualifiedErrorId : Operation failed because the specified MA could not be found.,Microsoft.IdentityManageme
   nt.PowerShell.Cmdlet.GetADSyncConnectorCmdlet

```

Rutiny Powershellu zaznamená chybu **nebyl nalezen zadaný MA**.

Z důvodu, že to nastane totiž aktuální konfiguraci Azure AD Connect není podporována pro upgrade. 

Pokud chcete nainstalovat novější verze služby Azure AD Connect: zavřete průvodce službou Azure AD Connect, odinstalujte existující Azure AD Connect a proveďte čistou instalaci novější Azure AD Connect.



## <a name="next-steps"></a>Další postup
Další informace o [integrace místních identit s Azure Active Directory](active-directory-aadconnect.md).

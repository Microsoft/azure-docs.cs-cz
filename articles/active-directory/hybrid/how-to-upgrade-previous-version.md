---
title: 'Azure AD Connect: Upgrade z předchozí verze | Dokumenty společnosti Microsoft'
description: Vysvětluje různé metody upgradu na nejnovější verzi služby Azure Active Directory Connect, včetně upgradu na místě a migrace švihákem.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 31f084d8-2b89-478c-9079-76cf92e6618f
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: Identity
ms.date: 04/08/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2a3e7373a8b0354a3d08debf944f2f77f1609382
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60347687"
---
# <a name="azure-ad-connect-upgrade-from-a-previous-version-to-the-latest"></a>Azure AD Connect: Upgrade z předchozí verze na nejnovější verzi
Toto téma popisuje různé metody, které můžete použít k upgradu instalace služby Azure Active Directory (Azure AD) Připojení na nejnovější verzi. Doporučujeme, abyste se udržovali aktuální s verzemi Azure AD Connect. Kroky v části [Migrace švihnutí](#swing-migration) se také používají, když provedete podstatnou změnu konfigurace.

>[!NOTE]
> Aktuálně se podporuje upgrade z libovolné verze služby Azure AD Connect na aktuální verzi. Inadická inovace dirsync nebo ADSync nejsou podporovány a je vyžadována migrace švihu.  Pokud chcete upgradovat z DirSync, najdete v článku [upgrade z nástroje synchronizace Azure AD (DirSync)](how-to-dirsync-upgrade-get-started.md) nebo v části [migrace Swing.](#swing-migration)  </br>V praxi se zákazníci v extrémně starých verzích mohou setkat s problémy, které přímo nesouvisejí s Azure AD Connect. Servery, které byly ve výrobě již několik let, obvykle měly několik oprav, které byly aplikovány na ně a ne všechny z nich mohou být účtovány.  Obecně platí, že zákazníci, kteří nemají upgrade v 12-18 měsíců by měl zvážit swing upgrade místo toho, jak to je nejkonzervativnější a nejméně riskantní možnost.

Pokud chcete upgradovat z DirSync, najdete v článku [Upgrade z nástroje synchronizace Azure AD (DirSync)](how-to-dirsync-upgrade-get-started.md) místo.

Existuje několik různých strategií, které můžete použít k upgradu Azure AD Connect.

| Metoda | Popis |
| --- | --- |
| [Automatický upgrade](how-to-connect-install-automatic-upgrade.md) |Jedná se o nejjednodušší metodu pro zákazníky s expresní instalací. |
| [Místní upgrade](#in-place-upgrade) |Pokud máte jeden server, můžete inovovat instalaci na stejném serveru. |
| [Postupná migrace](#swing-migration) |Se dvěma servery můžete připravit jeden ze serverů s novou verzí nebo konfigurací a změnit aktivní server, až budete připraveni. |

Informace o oprávněních naleznete v [tématu oprávnění požadovaná pro upgrade](reference-connect-accounts-permissions.md#upgrade).

> [!NOTE]
> Po povolení nového serveru Azure AD Connect pro zahájení synchronizace změn ve službě Azure AD se nesmíte vrátit zpět k používání DirSync nebo Azure AD Sync. Přechod ze služby Azure AD Connect na starší klienty, včetně DirSync a Azure AD Sync, není podporován a může vést k problémům, jako je ztráta dat ve službě Azure AD.

## <a name="in-place-upgrade"></a>Místní upgrade
Vlastní upgrade funguje pro přechod z Azure AD Sync nebo Azure AD Connect. Nefunguje pro přechod z DirSync nebo pro řešení s Forefront Identity Manager (FIM) + Azure AD Connector.

Tato metoda je upřednostňována, pokud máte jeden server a méně než 100 000 objektů. Pokud dojde ke změnám pravidel synchronizace předváděcí okno, dojde po upgradu k úplnému importu a úplné synchronizaci. Tato metoda zajišťuje, že nová konfigurace je použita pro všechny existující objekty v systému. Toto spuštění může trvat několik hodin, v závislosti na počtu objektů, které jsou v rozsahu synchronizačního modulu. Normální plánovač synchronizace delta (který synchronizuje každých 30 minut ve výchozím nastavení) je pozastavena, ale synchronizace hesla pokračuje. Můžete zvážit provedení in-place upgrade během víkendu. Pokud neexistují žádné změny v konfiguraci out-of-box s novou verzí Azure AD Connect, pak normální delta import/synchronizace spustí místo.  
![Místní upgrade](./media/how-to-upgrade-previous-version/inplaceupgrade.png)

Pokud jste provedli změny pravidel synchronizace předpoložky, pak tato pravidla jsou nastaveny zpět na výchozí konfiguraci při upgradu. Chcete-li se ujistit, že je vaše konfigurace mezi inovacemi zachována, proveďte změny tak, jak jsou popsány v [doporučených postupech pro změnu výchozí konfigurace](how-to-connect-sync-best-practices-changing-default-configuration.md).

Během inovace na místě mohou být zavedeny změny, které vyžadují konkrétní synchronizační aktivity (včetně kroku úplného importu a úplného kroku synchronizace), které mají být provedeny po dokončení upgradu. Chcete-li tyto aktivity odložit, naleznete v části [Jak odložit úplnou synchronizaci po upgradu](#how-to-defer-full-synchronization-after-upgrade).

Pokud používáte Azure AD Connect s nestandardníkonektor (například obecný ldap konektor a obecný konektor SQL), je nutné aktualizovat odpovídající konfiguraci konektoru ve [Správci služeb synchronizace](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-service-manager-ui-connectors) po inovaci na místě. Podrobnosti o aktualizaci konfigurace konektoru naleznete v článku [Historie verzí verzí konektoru – Poradce při potížích](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-connector-version-history#troubleshooting). Pokud konfiguraci neaktualizujete, kroky spuštění importu a exportu nebudou pro konektor fungovat správně. V protokolu událostí aplikace se zobrazí následující chyba se zprávou *"Verze sestavení v konfiguraci konektoru AAD ("X.X.XXX. X") je starší než skutečná verze ("X.X.XXX. X") z "C:\Program Files\Microsoft Azure AD Sync\Extensions\Microsoft.IAM.Connector.GenericLdap.dll".*

## <a name="swing-migration"></a>Postupná migrace
Pokud máte komplexní nasazení nebo mnoho objektů, může být nepraktické provést upgrade na místě v živém systému. U některých zákazníků může tento proces trvat několik dní a během této doby nejsou zpracovány žádné rozdílové změny. Tuto metodu můžete také použít, když plánujete provést podstatné změny konfigurace a chcete je vyzkoušet dříve, než jsou posunuty do cloudu.

Doporučená metoda pro tyto scénáře je použití migrace houpavého. Potřebujete (alespoň) dva servery – jeden aktivní server a jeden pracovní server. Aktivní server (zobrazený s plnými modrými čarami na následujícím obrázku) je zodpovědný za aktivní výrobní zatížení. Pracovní server (zobrazený s přerušovanými fialovými čarami) je připraven s novou verzí nebo konfigurací. Když je plně připraven, tento server je aktivní. Předchozí aktivní server, který má nyní nainstalovanou starou verzi nebo konfiguraci, je převeden na pracovní server a je inovován.

Oba servery mohou používat různé verze. Například aktivní server, který plánujete vyřazení z provozu můžete použít Azure AD Sync a nový pracovní server můžete použít Azure AD Connect. Pokud k vývoji nové konfigurace použijete migraci houpavého zařízení, je vhodné mít na obou serverech stejné verze.  
![Pracovní server](./media/how-to-upgrade-previous-version/stagingserver1.png)

> [!NOTE]
> Někteří zákazníci dávají přednost tři nebo čtyři servery pro tento scénář. Při upgradu pracovního serveru nemáte záložní server pro [zotavení po havárii](how-to-connect-sync-staging-server.md#disaster-recovery). Se třemi nebo čtyřmi servery můžete připravit jednu sadu primárních/pohotovostních serverů s novou verzí, která zajistí, že vždy existuje pracovní server, který je připraven k převzetí.

Tyto kroky také fungují k přechodu z Azure AD Sync nebo řešení s FIM + Azure AD Connector. Tyto kroky nefungují pro DirSync, ale stejná metoda migrace houpání (označovaná také jako paralelní nasazení) s kroky pro DirSync je v [synchronizaci služby Azure Active Directory (DirSync).](how-to-dirsync-upgrade-get-started.md)

### <a name="use-a-swing-migration-to-upgrade"></a>Upgrade pomocí migrace švihu
1. Pokud používáte Azure AD Connect na obou serverech a plánujete jenom provést změnu konfigurace, ujistěte se, že váš aktivní server a pracovní server používají stejnou verzi. To usnadňuje porovnání rozdílů později. Pokud upgradujete z Azure AD Sync, pak tyto servery mají různé verze. Pokud upgradujete ze starší verze Azure AD Connect, je vhodné začít se dvěma servery, které používají stejnou verzi, ale není to povinné.
2. Pokud jste provedli vlastní konfiguraci a pracovní server ji nemá, postupujte podle pokynů v části [Přesunutí vlastní konfigurace z aktivního serveru na pracovní server](#move-a-custom-configuration-from-the-active-server-to-the-staging-server).
3. Pokud upgradujete z dřívější verze Služby Azure AD Connect, upgradujte pracovní server na nejnovější verzi. Pokud přecházíte z Azure AD Sync, nainstalujte Azure AD Connect na pracovní server.
4. Nechte synchronizační modul spustit úplný import a úplnou synchronizaci na pracovním serveru.
5. Ověřte, zda nová konfigurace nezpůsobila žádné neočekávané změny pomocí kroků v části Ověření v [části Ověření konfigurace serveru](how-to-connect-sync-staging-server.md#verify-the-configuration-of-a-server). Pokud něco není podle očekávání, opravte to, spusťte import a synchronizaci a ověřte data, dokud nebude vypadat dobře, podle kroků.
6. Přepněte pracovní server na aktivní server. Toto je poslední krok "Přepnout aktivní server" v [Ověření konfigurace serveru](how-to-connect-sync-staging-server.md#verify-the-configuration-of-a-server).
7. Pokud upgradujete Azure AD Connect, upgradujte server, který je teď v pracovním režimu, na nejnovější verzi. Chcete-li upgradovat data a konfiguraci, postupujte stejným způsobem jako dříve. Pokud jste upgradovali z Azure AD Sync, můžete teď vypnout a vyřadit z provozu starý server.

### <a name="move-a-custom-configuration-from-the-active-server-to-the-staging-server"></a>Přesunutí vlastní konfigurace z aktivního serveru na pracovní server
Pokud jste provedli změny konfigurace aktivního serveru, musíte se ujistit, že stejné změny jsou použity na pracovní server. Chcete-li pomoci s tímto krokem, můžete použít [dokumentátor konfigurace Azure AD Connect](https://github.com/Microsoft/AADConnectConfigDocumenter).

Vlastní pravidla synchronizace, která jste vytvořili, můžete přesunout pomocí PowerShellu. Ostatní změny je nutné použít stejným způsobem v obou systémech a změny nelze migrovat. [Konfigurační dokumentátor](https://github.com/Microsoft/AADConnectConfigDocumenter) vám může pomoci porovnat oba systémy a ujistit se, že jsou identické. Nástroj může také pomoci při automatizaci kroků v této části.

Na obou serverech je třeba nakonfigurovat následující věci stejným způsobem:

* Připojení ke stejným doménovým strukturám
* Filtrování libovolné domény a ou.
* Stejné volitelné funkce, jako je synchronizace hesel a zpětný zápis hesla

**Přesunutí vlastních pravidel synchronizace**  
Chcete-li přesunout vlastní pravidla synchronizace, postupujte takto:

1. Sem **otevřete Editor pravidel synchronizace** na aktivním serveru.
2. Vyberte vlastní pravidlo. Klikněte na **Exportovat**. Tím se vyvolá okno poznámkového bloku. Uložte dočasný soubor s příponou PS1. Díky tomu je skript prostředí PowerShell. Zkopírujte soubor PS1 na pracovní server.  
   ![Export pravidel synchronizace](./media/how-to-upgrade-previous-version/exportrule.png)
3. Identifikátor GUID konektoru se na pracovním serveru liší a je nutné jej změnit. Chcete-li získat identifikátor GUID, **spusťte Editor pravidel synchronizace**, vyberte jedno z pravidel out-of-box, které představují stejný připojený systém, a klepněte na tlačítko **Exportovat**. Nahraďte identifikátor GUID v souboru PS1 identifikátorem GUID z pracovního serveru.
4. V příkazovém řádku prostředí PowerShell spusťte soubor PS1. Tím se vytvoří vlastní pravidlo synchronizace na pracovní server.
5. Tento postup opakujte pro všechna vlastní pravidla.

## <a name="how-to-defer-full-synchronization-after-upgrade"></a>Jak odložit úplnou synchronizaci po upgradu
Během inovace na místě mohou být zavedeny změny, které vyžadují provádění konkrétních synchronizačních aktivit (včetně kroku úplného importu a úplné synchronizace). Například změny schématu konektoru vyžadují úplný krok **importu** a změny pravidel synchronizace předboxu vyžadují provedení úplného kroku **synchronizace** na ovlivněných konektorech. Během upgradu Azure AD Connect určuje, jaké aktivity synchronizace jsou požadovány a zaznamenává je jako *přepsání*. V následujícím cyklu synchronizace plánovač synchronizace vyzvedne tato přepsání a provede je. Jakmile je přepsání úspěšně provedeno, je odebráno.

Mohou nastat situace, kdy nechcete, aby tato přepsání proběhla ihned po upgradu. Například máte mnoho synchronizovaných objektů a chcete, aby tyto kroky synchronizace dojít po pracovní době. Chcete-li odstranit tato přepsání:

1. Během upgradu **odškrtnete** možnost **Spusťte proces synchronizace po dokončení konfigurace**. Tím zakážete plánovač synchronizace a zabráníte automatickému cyklu synchronizace před odebráním přepsání.

   ![Zakázatupgrade FullSyncAfter](./media/how-to-upgrade-previous-version/disablefullsync01.png)

2. Po dokončení upgradu spusťte následující rutinu, abyste zjistili, jaká přepsání byla přidána:`Get-ADSyncSchedulerConnectorOverride | fl`

   >[!NOTE]
   > Přepsání jsou specifické pro konektor. V následujícím příkladu úplné import krok a úplné synchronizace krok byly přidány do místní ho aD konektor a Konektor Azure AD.

   ![Zakázatupgrade FullSyncAfter](./media/how-to-upgrade-previous-version/disablefullsync02.png)

3. Poznamenejte si existující přepsání, které byly přidány.
   
4. Chcete-li odstranit lokální změny pro úplný import i úplnou synchronizaci na libovolné spojnici, spusťte následující rutinu:`Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid-of-ConnectorIdentifier> -FullImportRequired $false -FullSyncRequired $false`

   Chcete-li odstranit lokální změny na všech konektorech, spusťte následující skript prostředí PowerShell:

   ```
   foreach ($connectorOverride in Get-ADSyncSchedulerConnectorOverride)
   {
       Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier $connectorOverride.ConnectorIdentifier.Guid -FullSyncRequired $false -FullImportRequired $false
   }
   ```

5. Chcete-li pokračovat v plánovači, spusťte následující rutinu:`Set-ADSyncScheduler -SyncCycleEnabled $true`

   >[!IMPORTANT]
   > Nezapomeňte provést požadované kroky synchronizace co nejdříve. Tyto kroky můžete provést ručně pomocí Správce synchronizačních služeb nebo přidat přepsání zpět pomocí rutiny Set-ADSyncSchedulerConnectorOverride.

Chcete-li přidat lokální změny pro úplný import i úplnou synchronizaci na libovolné spojnici, spusťte následující rutinu:`Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid> -FullImportRequired $true -FullSyncRequired $true`

## <a name="troubleshooting"></a>Řešení potíží
Následující část obsahuje řešení potíží a informace, které můžete použít, pokud narazíte na problém s upgradem služby Azure AD Connect.

### <a name="azure-active-directory-connector-missing-error-during-azure-ad-connect-upgrade"></a>Chybějící chyba konektoru Azure Active Directory během upgradu služby Azure AD Connect

Při upgradu Azure AD Connect z předchozí verze, může dojít k následující chyby na začátku upgradu 

![Chyba](./media/how-to-upgrade-previous-version/error1.png)

K této chybě dochází, protože konektor Azure Active Directory s identifikátorem, b891884f-051e-4a83-95af-2544101c9083, neexistuje v aktuální konfiguraci Azure AD Connect. Chcete-li ověřit tento případ, otevřete okno prostředí PowerShell, spusťte rutinu`Get-ADSyncConnector -Identifier b891884f-051e-4a83-95af-2544101c9083`

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

Rutina prostředí PowerShell hlásí **chybu, kterou zadaný ma nebyl nalezen**.

Důvodem, proč k tomu dochází, je, že aktuální konfigurace Azure AD Connect není podporována pro upgrade. 

Pokud chcete nainstalovat novější verzi Azure AD Connect: zavřete průvodce Azure AD Connect, odinstalujte stávající Azure AD Connect a proveďte čistou instalaci novějšího Azure AD Connect.



## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [integraci místních identit pomocí služby Azure Active Directory](whatis-hybrid-identity.md).

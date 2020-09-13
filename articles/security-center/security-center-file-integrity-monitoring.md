---
title: Monitorování integrity souborů v Azure Security Center | Microsoft Docs
description: Naučte se konfigurovat monitorování integrity souborů (FIM) v Azure Security Center pomocí tohoto návodu.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/13/2019
ms.author: memildin
ms.openlocfilehash: 910d98558e5b949a76202cce48c2a210531d5c35
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2020
ms.locfileid: "89459789"
---
# <a name="file-integrity-monitoring-in-azure-security-center"></a>Monitorování integrity souborů ve službě Azure Security Center
Naučte se konfigurovat monitorování integrity souborů (FIM) v Azure Security Center pomocí tohoto návodu.


## <a name="availability"></a>Dostupnost

|Aspekt|Podrobnosti|
|----|:----|
|Stav vydaných verzí:|Všeobecně dostupné|
|Stanov|Úroveň Standard|
|Požadované role a oprávnění:|**Vlastník pracovního prostoru** může povolit nebo zakázat FIM (Další informace najdete v tématu [role Azure pro Log Analytics](https://docs.microsoft.com/services-hub/health/azure-roles#azure-roles)).<br>**Čtenář** může zobrazit výsledky.|
|Cloud|![Yes](./media/icons/yes-icon.png) Komerční cloudy<br>![Yes](./media/icons/yes-icon.png) US Gov<br>![No](./media/icons/no-icon.png) Čína gov, jiné gov|
|||





## <a name="what-is-fim-in-security-center"></a>Co je FIM v Security Center?
Monitoring integrity souborů (FIM), označovaný také jako sledování změn, prověřuje soubory a Registry operačního systému, aplikačního softwaru a dalších pro změny, které by mohly naznačovat útok. Metoda porovnání se používá k určení, zda je aktuální stav souboru odlišný od poslední kontroly souboru. Toto porovnání můžete využít k určení, zda byly provedeny platné nebo podezřelé úpravy souborů.

Security Center monitorování integrity souborů ověřuje integritu souborů Windows, registru Windows a souborů systému Linux. Soubory, které chcete monitorovat, vyberete tak, že povolíte FIM. Security Center monitoruje soubory s povolenou službou FIM pro aktivity, jako například:

- Vytváření a odstraňování souborů a registru
- Úpravy souborů (změny velikosti souboru, seznamy řízení přístupu a hodnota hash obsahu)
- Změny registru (změny velikosti, seznamy řízení přístupu, typ a obsah)

Security Center doporučuje pro monitorované entity, na kterých můžete rychle povolit FIM. Můžete také definovat vlastní zásady FIM nebo entity, které se mají monitorovat. V tomto návodu se dozvíte, jak.

> [!NOTE]
> Funkce monitoring integrity souborů (FIM) funguje pro počítače se systémem Windows a Linux a je k dispozici na úrovni Standard Security Center. Další informace o cenových úrovních služby Security Center najdete na stránce s [cenami](security-center-pricing.md). FIM nahrává data do pracovního prostoru Log Analytics. Poplatky za data platí na základě objemu dat, která nahráváte. Další informace najdete v tématu [Log Analytics ceny](https://azure.microsoft.com/pricing/details/log-analytics/) .

FIM používá řešení Azure Change Tracking ke sledování a identifikaci změn ve vašem prostředí. Když je povolené monitorování integrity souborů, máte **Change Tracking** prostředek typu **řešení**. Podrobnosti o frekvenci shromažďování dat v řešení Azure Change Tracking najdete v [podrobnostech o shromažďování dat v řešení Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking#change-tracking-data-collection-details).

> [!NOTE]
> Pokud odeberete prostředek **Change Tracking** , zakážete tím také funkci monitorování integrity souborů v Security Center.

## <a name="which-files-should-i-monitor"></a>Které soubory mám monitorovat?
Při výběru souborů, které chcete monitorovat, byste si měli myslet na soubory, které jsou pro váš systém a aplikace kritické. Zvažte možnost zvolit soubory, které nebudete chtít měnit bez plánování. Výběr souborů, které jsou často měněny aplikacemi nebo operačním systémem (například soubory protokolů a textové soubory), vytvářejí velký šum, který obtížně identifikuje útok.

Security Center poskytuje následující seznam doporučených položek, které se mají monitorovat na základě známých vzorů útoků. Mezi ně patří tyto soubory a klíče registru Windows. Všechny klíče jsou pod HKEY_LOCAL_MACHINE ("HKLM" v tabulce.)

|**Soubory Linux**|**Soubory Windows**|**Klíče registru Windows**|
|:----|:----|:----|
|/bin/login|C:\autoexec.bat|HKLM\SOFTWARE\Microsoft\Cryptography\OID\EncodingType 0 \ CryptSIPDllRemoveSignedDataMsg \{ C689AAB8-8E78-11D0-8C47-00C04FC295EE}|
|/bin/passwd|C:\boot.ini|HKLM\SOFTWARE\Microsoft\Cryptography\OID\EncodingType 0 \ CryptSIPDllRemoveSignedDataMsg \{ 603BCC1F-4B59-4E08-B724-D2C6297EF351}|
|/etc/*. conf|C:\config.sys|HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\IniFileMapping\SYSTEM.ini \Boot.|
|/usr/bin|C:\Windows\system.ini|HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Windows|
|/usr/sbin|C:\Windows\win.ini|HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon|
|/bin|C:\Windows\regedit.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Shell složky|
|/sbin|C:\Windows\System32\userinit.exe|Složky prostředí HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User|
|/boot|C:\Windows\explorer.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run|
|/usr/local/bin|C:\Program Files\Microsoft – Client\msseces.exe zabezpečení|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce|
|/usr/local/sbin||HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnceEx|
|/opt/bin||HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunServices|
|/opt/sbin||HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunServicesOnce|
|/etc/crontab||HKLM\SOFTWARE\WOW6432Node\Microsoft\Cryptography\OID\EncodingType 0 \ CryptSIPDllRemoveSignedDataMsg \{ C689AAB8-8E78-11D0-8C47-00C04FC295EE}|
|/etc/init.d||HKLM\SOFTWARE\WOW6432Node\Microsoft\Cryptography\OID\EncodingType 0 \ CryptSIPDllRemoveSignedDataMsg \{ 603BCC1F-4B59-4E08-B724-D2C6297EF351}|
|/etc/cron.hourly||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows NT\CurrentVersion\IniFileMapping\system.ini \Boot.|
|/etc/cron.daily||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows NT\CurrentVersion\Windows|
|/etc/cron.weekly||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows NT\CurrentVersion\Winlogon|
|/etc/cron.monthly||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Explorer\Shell složky|
|||Složky prostředí HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Explorer\User|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Run|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunOnce|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunOnceEx|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunServices|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunServicesOnce|
|||HKLM\SYSTEM\CurrentControlSet\Control\hivelist|
|||HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\KnownDLLs|
|||HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\DomainProfile|
|||HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\PublicProfile|
|||HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\StandardProfile|

## <a name="using-file-integrity-monitoring"></a>Použití monitorování integrity souborů
1. Otevřete řídicí panel **Security Center**.
2. V levém podokně v části **Pokročilá obrana cloudu**vyberte **monitorování integrity souborů**.
![Řídicí panel Security Center][1]

Otevře se **monitorování integrity souborů** .
  ![Řídicí panel Security Center][2]

Pro každý pracovní prostor jsou k dispozici následující informace:

- Celkový počet změn, ke kterým došlo za poslední týden (pomlčka může být "–", pokud není v pracovním prostoru povolený produkt FIM)
- Celkový počet počítačů a virtuálních počítačů, které vytvářejí sestavy do pracovního prostoru
- Geografické umístění pracovního prostoru
- Předplatné Azure, pod kterým je pracovní prostor

Pro pracovní prostor můžete zobrazit také následující tlačítka:

- ![Ikona povolit][3] Indikuje, že produkt FIM není pro tento pracovní prostor povolený. Výběr pracovního prostoru vám umožní povolit FIM na všech počítačích v pracovním prostoru.
- ![Ikona plán upgradu ][4] indikuje, že pracovní prostor nebo předplatné neběží na úrovni standard Security Center. Aby bylo možné používat funkci FIM, musí vaše předplatné běžet standardně.  Výběr pracovního prostoru vám umožní upgradovat na úroveň Standard. Další informace o úrovni Standard a o tom, jak upgradovat, najdete v tématu [upgrade na úroveň Security Center úrovně Standard pro zvýšení zabezpečení](security-center-pricing.md).
- Prázdné (žádné tlačítko) znamená, že je v pracovním prostoru už povolený produkt FIM.

V části **monitorování integrity souborů**můžete vybrat pracovní prostor, pro který chcete povolit FIM pro tento pracovní prostor, zobrazit řídicí panel monitorování integrity souborů pro daný pracovní prostor nebo [upgradovat](security-center-pricing.md) pracovní prostor na úroveň Standard.

## <a name="enable-fim"></a>Povolit FIM
Povolení produktu FIM v pracovním prostoru:

1. V části **monitorování integrity souborů**vyberte pracovní prostor s tlačítkem **Povolit** .
2. **Možnost povolit monitorování integrity souborů** se otevře a zobrazí se počet počítačů se systémem Windows a Linux v pracovním prostoru.

   ![Povolit monitorování integrity souborů][5]

   Jsou uvedena také doporučená nastavení pro systémy Windows a Linux.  Pokud chcete zobrazit úplný seznam doporučených položek, rozbalte soubory **Windows**, **Registry**a **Linux Files** .

3. Zrušte kontrolu nad všemi doporučenými entitami, pro které nechcete používat FIM.
4. Pokud chcete povolit FIM, vyberte **použít monitorování integrity souborů** .

> [!NOTE]
> Nastavení můžete kdykoli změnit. Další informace najdete v tématu Úprava monitorovaných entit níže.


## <a name="view-the-fim-dashboard"></a>Zobrazení řídicího panelu FIM
Řídicí panel **monitorování integrity souborů** se zobrazí pro pracovní prostory, ve kterých je povolený FIM. Po povolení produktu FIM v pracovním prostoru nebo při výběru pracovního prostoru v okně **monitorování integrity souborů** , které už má povolený FIM, se otevře řídicí panel FIM.

![Řídicí panel monitorování integrity souborů][6]

Řídicí panel FIM pro pracovní prostor zobrazuje následující podrobnosti:

- Celkový počet počítačů připojených k pracovnímu prostoru
- Celkový počet změn, ke kterým došlo během vybraného časového období
- Rozpis typu změny (soubory, registr)
- Rozpis kategorie změn (upraveno, přidáno, odebráno)

Výběrem filtru v horní části řídicího panelu můžete použít časový úsek, pro který chcete zobrazit změny.

![Filtr časového období][7]

Karta **počítače** (zobrazené výše) obsahuje seznam všech počítačů, které se hlásí do tohoto pracovního prostoru. Pro každý počítač řídicí panel uvádí:

- Celkový počet změn, ke kterým došlo během vybraného časového období.
- Rozpis celkového počtu změn změn v souboru nebo změny v registru

**Hledání v protokolu** se spustí, když do vyhledávacího pole zadáte název počítače nebo vyberete počítač uvedený na kartě počítače. při hledání v protokolu se zobrazí všechny změny provedené během vybraného časového období pro daný počítač. Můžete rozšířit změnu pro další informace.

![Prohledávání protokolů][8]

Karta **změny** (uvedená níže) obsahuje seznam všech změn pracovního prostoru během vybraného časového období. Pro každou změněnou entitu zobrazuje řídicí panel:

- Počítač, na kterém došlo ke změně
- Typ změny (registr nebo soubor)
- Kategorie změny (změněno, přidáno, odebráno)
- Datum a čas změny

![Změny pracovního prostoru][9]

Možnost **změnit podrobnosti** se otevře při zadání změny do vyhledávacího pole nebo vyberte entitu, která je uvedena na kartě **změny** .

![Změnit podrobnosti][10]

## <a name="edit-monitored-entities"></a>Upravit monitorované entity

1. Vraťte se na **řídicí panel monitorování integrity souborů** a vyberte **Nastavení**.

   ![Nastavení][11]

   **Konfigurace pracovního prostoru** otevře tři karty: **Windows Registry**, **soubory Windows**a **Linux Files**. Každá karta obsahuje seznam entit, které můžete v této kategorii upravit. U každé entity uvedené Security Center identifikuje, jestli je FIM povolený (true) nebo není povolený (false).  Úprava entity umožňuje povolit nebo zakázat FIM.

   ![Konfigurace pracovního prostoru][12]

2. Vyberte ochranu identity. V tomto příkladu jsme vybrali položku v registru Windows. **Pro Change Tracking** se otevře dialogové okno Upravit.

   ![Upravit nebo změnit sledování][13]

V části **Upravit pro Change Tracking** můžete:

- Povolit (pravda) nebo zakázat (NEPRAVDA) monitorování integrity souborů
- Zadejte nebo změňte název entity.
- Zadejte nebo změňte hodnotu nebo cestu.
- Odstranit entitu, Zrušit změnu nebo uložit změnu

## <a name="add-a-new-entity-to-monitor"></a>Přidat novou entitu k monitorování
1. Vraťte se na **řídicí panel monitorování integrity souborů** a v horní části vyberte **Nastavení** . Otevře se **Konfigurace pracovního prostoru** .
2. V části **Konfigurace pracovního prostoru**vyberte kartu pro typ entity, kterou chcete přidat: Windows Registry, soubory Windows nebo soubory Linux. V tomto příkladu jsme vybrali **soubory pro Linux**.

   ![Přidat novou položku, která se má monitorovat][14]

3. Vyberte **Přidat**. Otevře se okno **Přidat pro Change Tracking** .

   ![Zadejte požadované informace.][15]

4. Na stránce **Přidat** zadejte požadované informace a vyberte **Uložit**.

## <a name="disable-monitored-entities"></a>Zakázat monitorované entity
1. Vraťte se na řídicí panel **monitorování integrity souborů** .
2. Vyberte pracovní prostor, ve kterém je FIM aktuálně povolený. Pro produkt FIM je povolen pracovní prostor, pokud mu chybí tlačítko Povolit nebo upgradovat plán.

   ![Vyberte pracovní prostor, ve kterém je FIM povolený.][16]

3. V části monitorování integrity souborů vyberte **Nastavení**.

   ![Výběr nastavení][17]

4. V části **Konfigurace pracovního prostoru**vyberte skupinu, ve které je **povolené** nastavení nastaveno na hodnotu true.

   ![Konfigurace pracovního prostoru][18]

5. V části **Upravit pro Change Tracking** sada okna **povolená** na hodnotu false.

   ![Nastavit jako povolené na false][19]

6. Vyberte **Uložit**.

## <a name="folder-and-path-monitoring-using-wildcards"></a>Monitorování složek a cest pomocí zástupných znaků

Pomocí zástupných znaků můžete zjednodušit sledování v adresářích. Při konfiguraci monitorování složek pomocí zástupných znaků platí následující pravidla:
-   Pro sledování více souborů jsou vyžadovány zástupné znaky.
-   Zástupné znaky se dají použít jenom v posledním segmentu cesty, jako je například C:\folder\file nebo/etc/*. conf.
-   Pokud proměnná prostředí obsahuje cestu, která není platná, ověření proběhne úspěšně, ale při spuštění inventáře selže cesta.
-   Při nastavení cesty Vyhněte obecným cestám, jako je například c: \* . *, což bude mít za následek příliš mnoho procházených složek.

## <a name="disable-fim"></a>Zakázat FIM
Můžete zakázat produkt FIM. FIM používá řešení Azure Change Tracking ke sledování a identifikaci změn ve vašem prostředí. Zakázáním produktu FIM odeberete Change Tracking řešení z vybraného pracovního prostoru.

1. Pokud chcete produkt FIM zakázat, vraťte se na řídicí panel **monitorování integrity souborů** .
2. Vyberte pracovní prostor.
3. V části **monitorování integrity souborů**vyberte **Zakázat**.

   ![Zakázat FIM][20]

4. Vyberte **Odebrat** a zakažte.

## <a name="next-steps"></a>Další kroky
V tomto článku jste se dozvěděli o použití monitorování integrity souborů (FIM) v Security Center. Další informace o Security Center najdete na následujících stránkách:

* [Nastavení zásad zabezpečení](tutorial-security-policy.md) – Naučte se konfigurovat zásady zabezpečení pro vaše předplatná Azure a skupiny prostředků.
* [Správa doporučení zabezpečení](security-center-recommendations.md) – Přečtěte si, jak doporučení vám pomůžou chránit vaše prostředky Azure.
* [Blog Azure Security](https://docs.microsoft.com/archive/blogs/azuresecurity/) – Získejte nejnovější informace o zabezpečení Azure.

<!--Image references-->
[1]: ./media/security-center-file-integrity-monitoring/security-center-dashboard.png
[2]: ./media/security-center-file-integrity-monitoring/file-integrity-monitoring.png
[3]: ./media/security-center-file-integrity-monitoring/enable.png
[4]: ./media/security-center-file-integrity-monitoring/upgrade-plan.png
[5]: ./media/security-center-file-integrity-monitoring/enable-fim.png
[6]: ./media/security-center-file-integrity-monitoring/fim-dashboard.png
[7]: ./media/security-center-file-integrity-monitoring/filter.png
[8]: ./media/security-center-file-integrity-monitoring/log-search.png
[9]: ./media/security-center-file-integrity-monitoring/changes-tab.png
[10]: ./media/security-center-file-integrity-monitoring/change-details.png
[11]: ./media/security-center-file-integrity-monitoring/fim-dashboard-settings.png
[12]: ./media/security-center-file-integrity-monitoring/workspace-config.png
[13]: ./media/security-center-file-integrity-monitoring/edit.png
[14]: ./media/security-center-file-integrity-monitoring/add.png
[15]: ./media/security-center-file-integrity-monitoring/add-item.png
[16]: ./media/security-center-file-integrity-monitoring/fim-dashboard-disable.png
[17]: ./media/security-center-file-integrity-monitoring/fim-dashboard-settings-disabled.png
[18]: ./media/security-center-file-integrity-monitoring/workspace-config-disable.png
[19]: ./media/security-center-file-integrity-monitoring/edit-disable.png
[20]: ./media/security-center-file-integrity-monitoring/disable-fim.png
---
title: Řešení potíží s připojením agenta s povolenými servery Azure ARC
description: V tomto článku se dozvíte, jak řešit problémy s agentem připojeného počítače, který se při pokusu o připojení ke službě vyskytnout u serverů s podporou ARC Azure.
ms.date: 04/12/2021
ms.topic: conceptual
ms.openlocfilehash: ae26b599a72129b5ed7f47d76d10353be5c0e8ac
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107497995"
---
# <a name="troubleshoot-azure-arc-enabled-servers-agent-connection-issues"></a>Řešení potíží s připojením agenta s povolenými servery Azure ARC

Tento článek poskytuje informace o řešení potíží a řešení problémů, ke kterým může dojít při pokusu o konfiguraci serverů s podporou Azure ARC připojeného agenta počítače pro Windows nebo Linux. Součástí jsou i metody interaktivního i navýšení instalace při konfiguraci připojení ke službě. Obecné informace najdete v tématu [Přehled serverů s podporou ARC](./overview.md).

## <a name="agent-error-codes"></a>Kódy chyb agenta

Pokud při konfiguraci agenta serverů s podporou ARC Azure dojde k chybě, může vám následující tabulka identifikovat pravděpodobnou příčinu a navrhované kroky k vyřešení vašeho problému. Abyste mohli pokračovat, budete potřebovat `AZCM0000` ("0000" může být jakékoli 4 číselné číslo). kód chyby je vytištěn do konzoly nebo na výstup skriptu.

| Kód chyby | Pravděpodobná příčina | Navrhovaná náprava |
|------------|----------------|-----------------------|
| AZCM0000 | Akce byla úspěšná. | – |
| AZCM0001 | Došlo k neznámé chybě. | Další pomoc vám poskytne podpora Microsoftu. |
| AZCM0011 | Uživatel zrušil akci (CTRL + C). | Opakovat předchozí příkaz |
| AZCM0012 | Zadaný přístupový token je neplatný. | Získejte nový přístupový token a zkuste to znovu. |
| AZCM0013 | Zadané značky jsou neplatné. | Ověřte, zda jsou značky uzavřeny v dvojitých uvozovkách oddělené čárkami a zda jsou názvy nebo hodnoty s mezerami uzavřeny v jednoduchých uvozovkách: `--tags "SingleName='Value with spaces',Location=Redmond"`
| AZCM0014 | Cloud je neplatný. | Zadejte podporovaný Cloud: `AzureCloud` nebo `AzureUSGovernment` |
| AZCM0015 | Zadané ID korelace není platný identifikátor GUID. | Zadejte platný identifikátor GUID pro `--correlation-id` |
| AZCM0016 | Chybí povinný parametr. | Zkontrolujte výstup a Identifikujte chybějící parametry. |
| AZCM0017 | Název prostředku je neplatný. | Zadejte název, který používá pouze alfanumerické znaky, spojovníky a podtržítka. Název nemůže končit spojovníkem nebo podtržítkem. |
| AZCM0018 | Příkaz se spustil bez oprávnění správce. | Opakujte příkaz s oprávněními správce nebo root v příkazovém řádku se zvýšenými oprávněními nebo v relaci konzoly. |
| AZCM0041 | Zadaná pověření jsou neplatná. | V případě přihlášení zařízení ověřte, že zadaný uživatelský účet má přístup k tenantovi a předplatnému, kde se prostředek serveru vytvoří. U přihlašovacích objektů instančního objektu ověřte správnost ID klienta a tajného klíče, datum vypršení platnosti tajného klíče a tento instanční objekt pochází ze stejného tenanta, ve kterém se prostředek serveru vytvoří. |
| AZCM0042 | Nepovedlo se vytvořit prostředek serveru s podporou ARC. | Ověřte, zda má zadaný uživatel nebo instanční objekt přístup k vytvoření prostředků serveru s povoleným obloukem v zadané skupině prostředků. |
| AZCM0043 | Nepovedlo se odstranit prostředek serveru s podporou ARC. | Ověřte, zda má zadaný uživatel nebo instanční objekt přístup k odstranění prostředků serveru s povoleným ARC v zadané skupině prostředků. Pokud prostředek již v Azure neexistuje, `--force-local-only` pokračujte pomocí příznaku. |
| AZCM0044 | Prostředek se stejným názvem už existuje. | Zadejte jiný název `--resource-name` parametru nebo odstraňte existující server s podporou ARC v Azure a zkuste to znovu. |
| AZCM0061 | Nepovedlo se kontaktovat službu agenta. | Ověřte, že je spuštěný příkaz v kontextu uživatele se zvýšenými oprávněními (správce/root) a že na vašem serveru běží služba HIMDS. |
| AZCM0062 | Při připojování k serveru došlo k chybě. | Podrobnější informace najdete v dalších kódech chyb ve výstupu. Pokud k chybě došlo po vytvoření prostředku Azure, musíte před opakováním pokusu odstranit ze skupiny prostředků Server ARC. |
| AZCM0063 | Při odpojování serveru došlo k chybě. | Podrobnější informace najdete v dalších kódech chyb ve výstupu. Pokud se tato chyba bude nacházet i nadále, můžete prostředek odstranit v Azure a pak `azcmagent disconnect --force-local-only` ho spustit na serveru a odpojit agenta. |
| AZCM0064 | Služba agenta neodpovídá. | Zkontrolujte stav `himds` služby a ujistěte se, že je spuštěná. Službu spusťte, pokud není spuštěná. Pokud je spuštěný, počkejte minutu a pak to zkuste znovu. |
| AZCM0065 | Došlo k vnitřní chybě komunikace agenta. | Požádat o pomoc podpora Microsoftu |
| AZCM0066 | Webová služba agenta neodpovídá nebo není k dispozici. | Požádat o pomoc podpora Microsoftu |
| AZCM0067 | Agent je už připojený k Azure. | Postupujte podle kroků v části [odpojení agenta](manage-agent.md#unregister-machine) a akci opakujte. |
| AZCM0068 | Při odpojování serveru od Azure došlo k vnitřní chybě. | Požádat o pomoc podpora Microsoftu |
| AZCM0081 | Při stahování Azure Active Directory spravovaného certifikátu identity došlo k chybě. | Pokud k této zprávě dojde při pokusu o připojení serveru k Azure, Agent nebude moct komunikovat se službou Azure ARC. Odstraňte prostředek v Azure a zkuste se znovu připojit. |
| AZCM0101 | Příkaz se nepovedlo úspěšně analyzovat. | Spusťte `azcmagent <command> --help` pro kontrolu správné syntaxe příkazu |
| AZCM0102 | Nepovedlo se načíst název hostitele počítače. | Spusťte `hostname` , chcete-li vyhledat všechny chybové zprávy na úrovni systému, a potom se obraťte na podpora Microsoftu. |
| AZCM0103 | Při generování klíčů RSA došlo k chybě. | Požádat o pomoc podpora Microsoftu |
| AZCM0104 | Nepovedlo se přečíst systémové informace. | Ověřte, že identita použitá ke spuštění `azcmagent` má v systému oprávnění správce/root, a zkuste to znovu. |

## <a name="agent-verbose-log"></a>Podrobný protokol agenta

Než budete postupovat podle kroků, které jsou popsané dále v tomto článku, je potřeba mít k tomu minimální informace v podrobném protokolu. Obsahuje výstup příkazů nástroje **azcmagent** při použití argumentu verbose (-v). Soubory protokolu jsou zapisovány do `%ProgramData%\AzureConnectedMachineAgent\Log\azcmagent.log` systému Windows a Linux do nástroje `/var/opt/azcmagent/log/azcmagent.log` .

### <a name="windows"></a>Windows

Následuje příklad příkazu, který umožňuje podrobné protokolování s připojeným agentem počítače pro Windows při provádění interaktivní instalace.

```console
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --verbose
```

Následuje příklad příkazu, který umožňuje podrobné protokolování s připojeným agentem počítače pro Windows při provádění instalace v rámci škálování pomocí instančního objektu.

```console
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect `
  --service-principal-id "{serviceprincipalAppID}" `
  --service-principal-secret "{serviceprincipalPassword}" `
  --resource-group "{ResourceGroupName}" `
  --tenant-id "{tenantID}" `
  --location "{resourceLocation}" `
  --subscription-id "{subscriptionID}"
  --verbose
```

### <a name="linux"></a>Linux

Následuje příklad příkazu, který umožňuje podrobné protokolování s připojeným agentem počítače pro Linux při provádění interaktivní instalace.

>[!NOTE]
>Aby bylo možné spustit **azcmagent**, musíte mít oprávnění *root* Access pro počítače se systémem Linux.

```bash
azcmagent connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --verbose
```

Následuje příklad příkazu, který umožňuje podrobné protokolování s připojeným agentem počítače pro Linux při provádění instalace v rámci škálování pomocí instančního objektu.

```bash
azcmagent connect \
  --service-principal-id "{serviceprincipalAppID}" \
  --service-principal-secret "{serviceprincipalPassword}" \
  --resource-group "{ResourceGroupName}" \
  --tenant-id "{tenantID}" \
  --location "{resourceLocation}" \
  --subscription-id "{subscriptionID}"
  --verbose
```

## <a name="agent-connection-issues-to-service"></a>Problémy s připojením agenta ke službě

V následující tabulce jsou uvedené některé známé chyby a návrhy, jak je řešit a řešit.

|Zpráva |Chyba |Pravděpodobná příčina |Řešení |
|--------|------|---------------|---------|
|Nepovedlo se získat tok zařízení autorizačního tokenu. |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp 40.126.9.7:443: connect: network is unreachable.` |Koncový bod není dostupný. `login.windows.net` | Ověřte připojení ke koncovému bodu. |
|Nepovedlo se získat tok zařízení autorizačního tokenu. |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp 40.126.9.7:443: connect: network is Forbidden`. |Proxy server nebo brána firewall blokuje přístup ke `login.windows.net` koncovému bodu. | Ověřte připojení ke koncovému bodu, které není blokované bránou firewall nebo proxy server. |
|Nepovedlo se získat tok zařízení autorizačního tokenu.  |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp lookup login.windows.net: no such host`. | Zásady skupiny objekt *Konfigurace počítače \ Šablony pro správu \ System \ profily uživatelů \ možnost Odstranit profily uživatelů starší, než je zadaný počet dnů při restartování systému* je povolená. | Ověřte, jestli je objekt zásad skupiny povolený a cílí na příslušný počítač. Další podrobnosti najdete v poznámce pod čarou <sup>[1](#footnote1)</sup> . |
|Nepovedlo se získat autorizační token ze SPN. |`Failed to execute the refresh request. Error = 'Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/token?api-version=1.0: Forbidden'` |Proxy server nebo brána firewall blokuje přístup ke `login.windows.net` koncovému bodu. |Ověřte připojení ke koncovému bodu, které není blokované bránou firewall nebo proxy server. |
|Nepovedlo se získat autorizační token ze SPN. |`Invalid client secret is provided` |Chybný nebo neplatný tajný klíč instančního objektu. |Ověřte tajný klíč instančního objektu. |
| Nepovedlo se získat autorizační token ze SPN. |`Application with identifier 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' was not found in the directory 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'. This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant` |Nesprávný instanční objekt nebo ID tenanta. |Ověřte instanční objekt nebo ID tenanta.|
|Získat odpověď na prostředek ARM |`The client 'username@domain.com' with object id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' does not have authorization to perform action 'Microsoft.HybridCompute/machines/read' over scope '/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.HybridCompute/machines/MSJC01' or the scope is invalid. If access was recently granted, please refresh your credentials."}}" Status Code=403` |Nesprávná pověření nebo oprávnění |Ověřte, že jste vy nebo instanční objekt je členem role registrace **počítače připojeného k Azure** . |
|Nepovedlo se AzcmagentConnectovat prostředek ARM. |`The subscription is not registered to use namespace 'Microsoft.HybridCompute'` |Poskytovatelé prostředků Azure nejsou zaregistrovaní. |Zaregistrujte [poskytovatele prostředků](./agent-overview.md#register-azure-resource-providers). |
|Nepovedlo se AzcmagentConnectovat prostředek ARM. |`Get https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.HybridCompute/machines/MSJC01?api-version=2019-03-18-preview:  Forbidden` |Proxy server nebo brána firewall blokují přístup ke `management.azure.com` koncovému bodu. |Ověřte připojení ke koncovému bodu, které není blokované bránou firewall nebo proxy server. |

<a name="footnote1"></a><sup>1</sup> Pokud je tento objekt zásad skupiny povolený a vztahuje se na počítače s připojeným agentem počítače, odstraní se profil uživatele přidružený k předdefinovanému účtu zadanému pro službu *himds* . V důsledku toho také odstraní ověřovací certifikát používaný ke komunikaci se službou uloženou v mezipaměti místního úložiště certifikátů po dobu 30 dnů. Před uplynutím 30denní lhůty se provede pokus o obnovení certifikátu. Pokud chcete tento problém vyřešit, použijte postup [zrušení registrace počítače](manage-agent.md#unregister-machine) a pak ho znovu zaregistrujte se spuštěnou službou `azcmagent connect` .

## <a name="next-steps"></a>Další kroky

Pokud tady nevidíte svůj problém nebo nemůžete problém vyřešit, zkuste další podporu vyzkoušet u některého z následujících kanálů:

* Získejte odpovědi od odborníků na Azure prostřednictvím [Microsoft Q&A](/answers/topics/azure-arc.html).

* Připojte se pomocí [@AzureSupport](https://twitter.com/azuresupport) oficiálního Microsoft Azure účtu pro zlepšení prostředí pro zákazníky. Podpora Azure spojuje komunitu Azure s odpověďmi, podporou a odborníky.

* Zasouborové incidenty podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/)a vyberte **získat podporu**.

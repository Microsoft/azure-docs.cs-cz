Postup zrušení registrace procesového serveru se liší v závislosti na jeho stavu připojení s konfiguračním serverem.

### <a name="unregister-a-process-server-that-is-in-a-connected-state"></a>Zrušení registrace procesového serveru, který je v připojeném stavu

1. Vzdáleně se připojte k procesovému server jako správce.
2. Spusťte **ovládací panely** a otevřete **Programy > Odinstalovat program**.
3. Odinstalujte program s názvem **Microsoft Azure Site Recovery Configuration/Process Server**.
4. Po dokončení kroku 3 můžete odinstalovat **závislosti pro Microsoft Azure Site Recovery Configuration/Process Server**.

### <a name="unregister-a-process-server-that-is-in-a-disconnected-state"></a>Zrušení registrace procesového serveru, který je v odpojeném stavu

> [!WARNING]
> Uvedené pokyny by se měly použít, pokud neexistuje žádný způsob jak oživit virtuální počítač, na kterém byl nainstalován na procesní server.

1. Přihlaste se ke konfiguračnímu serveru jako správce.
2. Otevřete příkazový řádek pro správu a přejděte do adresáře `%ProgramData%\ASR\home\svsystems\bin`.
3. Nyní spusťte příkaz.

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
4. Tím se vymažou podrobnosti o procesovém serveru ze systému.

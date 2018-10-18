## <a name="create-a-project-zip-file"></a>Vytvořit souboru ZIP projektu

Ověřte, že jste pořád v kořenovém adresáři ukázkového projektu. Vytvořte archiv ZIP se všemi položkami ve vašem projektu. Následující příkaz využívá základní nástroj vašeho terminálu:

```
# Bash
zip -r myAppFiles.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath myAppFiles.zip
``` 

Později tento soubor ZIP nahrajete do Azure a nasadíte pomocí služby App Service.
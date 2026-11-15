# cr-e-l-environnement-virtuel
Voici le script complet pour PowerShell : il crée l’environnement virtuel si nécessaire, l’active, installe les bibliothèques, crée AAPL.csv, puis démarre Jupyter.
Si .venv existe déjà, le script le réutilise sans le recréer.
# Aller dans le dossier du projet
$Project = "$HOME\Documents\AAPL_Intelligent_Trading"
New-Item -ItemType Directory -Force -Path $Project | Out-Null
Set-Location $Project

# Créer l'environnement virtuel s'il n'existe pas encore
if (-not (Test-Path ".\.venv\Scripts\python.exe")) {
    py -3 -m venv .venv
}

# Activer l'environnement virtuel
& ".\.venv\Scripts\Activate.ps1"

# Installer/mettre à jour pip et les bibliothèques nécessaires
python -m pip install --upgrade pip
python -m pip install jupyter ipykernel yfinance pandas matplotlib

# Télécharger environ un an de cours AAPL et créer AAPL.csv
python -c "import yfinance as yf; d=yf.download('AAPL', period='1y', auto_adjust=True, progress=False); print('Lignes téléchargées :', len(d)); if d.empty: raise SystemExit('Aucune donnée reçue : vérifiez Internet ou réessayez plus tard.'); d.to_csv('AAPL.csv'); print('Fichier créé :', __import__('pathlib').Path('AAPL.csv').resolve())"

# Vérifier que le CSV existe
Get-Item .\AAPL.csv
Remarque : la commande de téléchargement en une ligne ci-dessus peut provoquer une erreur de syntaxe PowerShell à cause du if dans le code Python. Utilisez plutôt cette version à plusieurs lignes pour la partie CSV :

# Ouvrir Jupyter Notebook
python -m jupyter notebook

@'
import pathlib
import yfinance as yf

data = yf.download("AAPL", period="1y", auto_adjust=True, progress=False)
print("Lignes téléchargées :", len(data))

if data.empty:
    raise SystemExit("Aucune donnée reçue : vérifiez Internet ou réessayez plus tard.")

output = pathlib.Path("AAPL.csv")
data.to_csv(output)
print("Fichier créé :", output.resolve())
'@ | python
Remplacez la ligne python -c "..." du script complet par ce bloc. Si PowerShell bloque l’activation, exécutez dans cette fenêtre :

Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass


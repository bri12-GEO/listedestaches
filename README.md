<?php

$tabTache = [];

if (file_exists("tache.json")) {
    $jsoncontent = file_get_contents("tache.json");
    $tabTache = json_decode($jsoncontent, true) ?? [];
}


$tache_a_modifier = [];
if (isset($_GET['indiceM']) && isset($tabTache[$_GET['indiceM']])) {
    $tache_a_modifier = $tabTache[$_GET['indiceM']];
}


if (isset($_POST['btnAjout'])) {
    $tache = [
        "titre" => $_POST['titre'],
        "description" => $_POST['description'],
        "statut" => $_POST['statut']
    ];

    if (isset($_GET['indiceM'])) {
        
        $tabTache[$_GET['indiceM']] = $tache;
    } else {
        
        $tabTache[] = $tache;
    }

    
    file_put_contents("tache.json", json_encode($tabTache, JSON_PRETTY_PRINT));

  
    header("Location: tp2.php");
    exit;
}

if (isset($_GET['indice']) && isset($tabTache[$_GET['indice']])) {
    array_splice($tabTache, $_GET['indice'], 1);
    file_put_contents("tache.json", json_encode($tabTache, JSON_PRETTY_PRINT));
    header("Location: tp2.php");
    exit;
}
?>

<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Gestion des tâches</title>
    <link rel="stylesheet" href="css/bootstrap.css">
</head>
<body>
<div class="container mt-4 pb-5">
    <h1 class="text-center text-primary">Gestion des tâches</h1>

    
    <div class="card mx-auto mt-4" style="max-width: 600px;">
        <div class="card-header bg-primary text-white fw-semibold">
            <?= isset($_GET['indiceM']) ? "Modifier Tâche" : "Ajouter Tâche" ?>
        </div>
        <div class="card-body">
            <form method="POST">
                <div class="mb-3">
                    <label for="titre" class="form-label">Titre de la tâche</label>
                    <input type="text" class="form-control" id="titre" name="titre"
                           value="<?= $tache_a_modifier['titre'] ?? '' ?>" required>
                </div>
                <div class="mb-3">
                    <label for="description" class="form-label">Description</label>
                    <textarea class="form-control" id="description" name="description" rows="3"
                              required><?= $tache_a_modifier['description'] ?? '' ?></textarea>
                </div>
                <div class="mb-3">
                    <label for="statut" class="form-label">Statut</label>
                    <select class="form-select" id="statut" name="statut" required>
                        <option value="En cours" <?= (isset($tache_a_modifier['statut']) && $tache_a_modifier['statut'] == "En cours") ? "selected" : "" ?>>En cours</option>
                        <option value="Terminée" <?= (isset($tache_a_modifier['statut']) && $tache_a_modifier['statut'] == "Terminée") ? "selected" : "" ?>>Terminée</option>
                        <option value="En attente" <?= (isset($tache_a_modifier['statut']) && $tache_a_modifier['statut'] == "En attente") ? "selected" : "" ?>>En attente</option>
                    </select>
                </div>
                <button type="submit" name="btnAjout" class="btn btn-success">
                    <?= isset($_GET['indiceM']) ? "Modifier" : "Ajouter une tâche" ?>
                </button>
            </form>
        </div>
    </div>

   
    <div class="mt-5">
        <h2 class="text-primary">Liste des tâches</h2>
    </div>

    <?php if (empty($tabTache)): ?>
        <div class="alert alert-info">Aucune tâche pour le moment</div>
    <?php else: ?>
        <div class="row g-3">
            <?php foreach ($tabTache as $key => $tache): ?>
                <div class="col-12 col-md-6 col-lg-4">
                    <div class="card shadow-sm h-100">
                        <div class="card-body d-flex flex-column">
                            <h5 class="card-title fw-semibold"><?= htmlspecialchars($tache['titre']) ?></h5>
                            <p class="card-text flex-grow-1"><?= htmlspecialchars($tache['description']) ?></p>

                            <div class="d-flex gap-2 align-items-center mb-2">
    <?php if ($tache['statut'] === "Terminée"): ?>
        <span class="badge bg-success">Terminée</span>
    <?php else: ?>
        <span class="badge bg-warning ">En cours</span>
    <?php endif; ?>
</div>

                             <hr>
                            <div class="d-flex gap-2">
                                <a href="TP2.php?indiceM=<?= $key ?>" class="btn btn-sm btn-primary">Modifier</a>
                                <a href="TP2.php?indice=<?= $key ?>"
                                   onclick="return confirm('Voulez-vous supprimer cette tâche ?')"
                                   class="btn btn-sm btn-danger">Supprimer</a>
                            </div>
                            
                        </div>
                    </div>
                </div>
            <?php endforeach; ?>
        </div>
    <?php endif; ?>
</div>
</body>
</html>

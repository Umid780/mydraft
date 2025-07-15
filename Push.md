# mydraft
optional

<!DOCTYPE html>
<html lang="uz">
<head>
    <meta charset="UTF-8">
    <title>Қишлоқ Хўжалиги Бозори</title>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css">
    <link rel="stylesheet" href="/static/css/style.css">
</head>
<body>
<nav class="navbar navbar-expand-lg navbar-light bg-light">
  <div class="container-fluid">
    <a class="navbar-brand" href="/">AgroMarket</a>
    <form class="d-flex" method="get">
        <input class="form-control me-2" type="search" placeholder="Mahsulot qidiring" name="q">
        <select name="region" class="form-select">
            <option value="">Barcha hududlar</option>
            <option value="Toshkent">Toshkent</option>
            <option value="Andijon">Andijon</option>
        </select>
        <button class="btn btn-outline-success ms-2" type="submit">Qidirish</button>
    </form>
  </div>
</nav>

<div class="container mt-4">
    <div class="row">
        {% for p in products %}
        <div class="col-md-4">
            <div class="card mb-4">
                <img src="{{ p.image.url }}" class="card-img-top" alt="{{ p.name }}">
                <div class="card-body">
                    <h5 class="card-title">{{ p.name }}</h5>
                    <p class="card-text">Narxi: {{ p.price }} so'm</p>
                    <p>{{ p.region }}</p>
                    <button class="btn btn-success">Sotib olish</button>
                </div>
            </div>
        </div>
        {% endfor %}
    </div>
</div>
</body>
</html>

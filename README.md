Step 1: Install Laravel

composer create-project laravel/laravel Laravel-Dynamically-Generate-Sitemap-XML-File

or laravel new Laravel-Dynamically-Generate-Sitemap-XML-File


Step 2: Create Post Migration and Model

php artisan make:migration create_posts_table

database/migrations/create_posts_table.php


php artisan migrate

php artisan make:model Post

app/Models/Post.php

protected $fillable = [
'title', 'slug', 'body'
];

Step 3: Create Post Factory

php artisan make:factory PostFactory

database/factories/PostFactory.php

public function definition()
{
return [
'title' => $this->faker->text(),
'slug' => Str::slug($this->faker->text()),
'body' => $this->faker->paragraph()
];
}

php artisan tinker

App\Models\Post::factory()->count(30)->create();

Step 4: Create Route

routes/web.php

Route::get('sitemap.xml', [SitemapController::class, 'index']);


Step 5: Create Controller

app/Http/Controllers/SitemapController.php

public function index($value='')
{
$posts = Post::latest()->get();
        return response()->view('sitemap', [
            'posts' => $posts
        ])->header('Content-Type', 'text/xml');
    }


Step 6: Create View File

resources/views/sitemap.blade.php

<?php echo '<?xml version="1.0" encoding="UTF-8"?>'; ?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
    @foreach ($posts as $post)
        <url>
            <loc>{{ url('/') }}/post/{{ $post->slug }}</loc>
            <lastmod>{{ $post->created_at->tz('UTC')->toAtomString() }}</lastmod>
            <changefreq>daily</changefreq>
            <priority>0.8</priority>
        </url>
    @endforeach
</urlset>




php artisan serve

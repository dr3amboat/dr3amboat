- 👋 Hi, I’m @dr3amboat
- 👀 I’m interested in Web Development/Data Analysis
- 🌱 I’m currently learning Web Development 
- 💞️ I’m looking to collaborate on ...
- 📫 How to reach me ...

<!---
dr3amboat/dr3amboat is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->
import android.os.Bundle
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.compose.foundation.clickable
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.lazy.LazyColumn
import androidx.compose.material.*
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.ArrowBack
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.unit.dp
import androidx.navigation.NavController
import androidx.navigation.compose.*

data class Recipe(
    val id: Int,
    val name: String,
    val category: String,
    val ingredients: List<String>,
    val rating: Float
)

val sampleRecipes = listOf(
    Recipe(
        id = 1,
        name = "Spaghetti Bolognese",
        category = "Italian",
        ingredients = listOf("Spaghetti", "Ground Beef", "Tomato Sauce", "Onion", "Garlic"),
        rating = 4.5f
    ),
    Recipe(
        id = 2,
        name = "Chicken Curry",
        category = "Indian",
        ingredients = listOf("Chicken", "Curry Powder", "Coconut Milk", "Onion", "Garlic"),
        rating = 4.0f
    ),
    Recipe(
        id = 3,
        name = "Sushi",
        category = "Japanese",
        ingredients = listOf("Rice", "Nori", "Salmon", "Avocado", "Soy Sauce"),
        rating = 5.0f
    )
)

class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            RecipeApp()
        }
    }
}

@Composable
fun RecipeApp() {
    val navController = rememberNavController()
    NavHost(navController, startDestination = "recipe_list") {
        composable("recipe_list") {
            RecipeListScreen(recipes = sampleRecipes, navController = navController)
        }
        composable("recipe_detail/{recipeId}") { backStackEntry ->
            val recipeId = backStackEntry.arguments?.getString("recipeId")?.toIntOrNull()
            val recipe = sampleRecipes.find { it.id == recipeId }
            recipe?.let {
                RecipeDetailScreen(recipe = it, navController = navController)
            }
        }
    }
}

@Composable
fun RecipeListScreen(recipes: List<Recipe>, navController: NavController) {
    Scaffold(
        topBar = {
            TopAppBar(title = { Text("Recipe List") })
        }
    ) { paddingValues ->
        LazyColumn(contentPadding = paddingValues) {
            items(recipes.size) { index ->
                val recipe = recipes[index]
                Card(
                    modifier = Modifier
                        .padding(8.dp)
                        .fillMaxWidth()
                        .clickable { navController.navigate("recipe_detail/${recipe.id}") },
                    elevation = 4.dp
                ) {
                    Column(modifier = Modifier.padding(16.dp)) {
                        Text(recipe.name, style = MaterialTheme.typography.h6)
                        Text("Category: ${recipe.category}")
                        RatingBar(rating = recipe.rating)
                    }
                }
            }
        }
    }
}

@Composable
fun RecipeDetailScreen(recipe: Recipe, navController: NavController) {
    Scaffold(
        topBar = {
            TopAppBar(
                navigationIcon = {
                    IconButton(onClick = { navController.popBackStack() }) {
                        Icon(Icons.Default.ArrowBack, contentDescription = "Back")
                    }
                },
                title = { Text(recipe.name) }
            )
        }
    ) { paddingValues ->
        Column(
            modifier = Modifier
                .padding(paddingValues)
                .padding(16.dp)
                .fillMaxSize()
        ) {
            Text("Category: ${recipe.category}", style = MaterialTheme.typography.subtitle1)
            Spacer(modifier = Modifier.height(8.dp))
            Text("Ingredients:", style = MaterialTheme.typography.subtitle2)
            recipe.ingredients.forEach { ingredient ->
                Text("- $ingredient")
            }
            Spacer(modifier = Modifier.height(16.dp))
            Text("Rating:")
            RatingBar(rating = recipe.rating)
        }
    }
}

@Composable
fun RatingBar(rating: Float, maxRating: Int = 5) {
    Row(verticalAlignment = Alignment.CenterVertically) {
        repeat(maxRating) { i ->
            Icon(
                imageVector = Icons.Default.ArrowBack, // Replace with star icon or custom star
                contentDescription = null,
                tint = if (i < rating.toInt()) MaterialTheme.colors.primary else MaterialTheme.colors.onSurface.copy(alpha = 0.3f)
            )
        }
        Spacer(modifier = Modifier.width(8.dp))
        Text("$rating/5")
    }
}

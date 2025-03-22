package com.example.labwork22

import android.os.Bundle
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.activity.enableEdgeToEdge
import androidx.compose.foundation.background
import androidx.compose.foundation.clickable
import androidx.compose.foundation.layout.Arrangement
import androidx.compose.foundation.layout.Box
import androidx.compose.foundation.layout.Column
import androidx.compose.foundation.layout.Row
import androidx.compose.foundation.layout.Spacer
import androidx.compose.foundation.layout.fillMaxSize
import androidx.compose.foundation.layout.fillMaxWidth
import androidx.compose.foundation.layout.height
import androidx.compose.foundation.layout.padding
import androidx.compose.foundation.layout.size
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Call
import androidx.compose.material.icons.filled.Email
import androidx.compose.material3.Button
import androidx.compose.material3.CircularProgressIndicator
import androidx.compose.material3.Icon
import androidx.compose.material3.LinearProgressIndicator
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.Scaffold
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.runtime.getValue
import androidx.compose.runtime.mutableStateOf
import androidx.compose.runtime.remember
import androidx.compose.runtime.rememberCoroutineScope
import androidx.compose.runtime.setValue
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.graphics.vector.ImageVector
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.text.style.TextAlign
import androidx.compose.ui.tooling.preview.Preview
import androidx.compose.ui.unit.dp
import androidx.compose.ui.unit.sp
import com.example.labwork22.ui.theme.LabWork22Theme
import kotlinx.coroutines.delay
import kotlinx.coroutines.launch
import kotlin.random.Random

class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            MyApp()
        }
    }
}

@Composable
fun MyApp() {
    MaterialTheme {
        DownloadScreen()
//        DownloadScreen2()
//        TimerScreen()
//        CounterScreen()
    }
}

@Composable
fun DownloadScreen() {
    var isDownloading by remember { mutableStateOf(false) }
    val coroutineScope = rememberCoroutineScope()

    Column(
        modifier = Modifier
            .fillMaxSize()
            .padding(16.dp),
        verticalArrangement = Arrangement.Center,
        horizontalAlignment = Alignment.CenterHorizontally
    ) {
        Button(
            onClick = {
                isDownloading = true
                coroutineScope.launch {
                    delay(10000)
                    isDownloading = false
                }
            },
            enabled = !isDownloading
        ) {
            Text("Скачать")
        }

        Spacer(modifier = Modifier.height(16.dp))

        if (isDownloading) {
            CircularProgressIndicator()
        } else {
            Text("Нажмите 'Скачать', чтобы начать загрузку.")
        }
    }
}

@Composable
fun DownloadScreen2() {
    Column(
        modifier = Modifier
            .fillMaxSize()
            .padding(16.dp),
        verticalArrangement = Arrangement.spacedBy(16.dp),
        horizontalAlignment = Alignment.CenterHorizontally
    ) {
        DownloadIndicator(color = Color.Blue)
        DownloadIndicator(color = Color.Green)
        DownloadIndicator(color = Color.Red)
    }
}

@Composable
fun DownloadIndicator(color: Color) {
    var progress by remember { mutableStateOf(0f) }
    var isDownloading by remember { mutableStateOf(false) }
    val coroutineScope = rememberCoroutineScope()

    Column(
        horizontalAlignment = Alignment.CenterHorizontally
    ) {
        Button(
            onClick = {
                isDownloading = true
                progress = 0f // Reset progress
                coroutineScope.launch {
                    while (progress < 1f) {
                        delay(100)
                        val randomIncrement = Random.nextFloat() * 0.1f // Random increment up to 10%
                        progress = (progress + randomIncrement).coerceIn(0f, 1f) // Ensure progress stays within 0-1
                    }
                    isDownloading = false
                }
            },
            enabled = !isDownloading
        ) {
            Text("Скачать")
        }

        Spacer(modifier = Modifier.height(8.dp))

        Text(text = "${(progress * 100).toInt()}%")

        LinearProgressIndicator(
            progress = progress,
            modifier = Modifier.fillMaxWidth(),
            color = color,
        )
    }
}

@Composable
fun TimerScreen() {
    var timeLeft by remember { mutableStateOf(60) } // Time in seconds
    var isRunning by remember { mutableStateOf(false) }
    val coroutineScope = rememberCoroutineScope()
    val strokeWidth = 12.dp // Толщина полоски

    val progress = (timeLeft.toFloat() / 60f).coerceIn(0f, 1f)

    val indicatorColor = when {
        timeLeft in 30..60 -> Color.Green
        timeLeft in 10..29 -> Color.Yellow
        timeLeft in 0..9 -> Color.Red
        else -> Color.Gray // For the time outside 0-60
    }

    Column(
        horizontalAlignment = Alignment.CenterHorizontally
    ) {
        Text(
            text = formatTime(timeLeft),
            fontSize = 48.sp,
            fontWeight = FontWeight.Bold
        )
        Spacer(modifier = Modifier.height(24.dp))

        CircularProgressIndicator(
            progress = progress,
            modifier = Modifier.size(200.dp), // Размер индикатора
            color = indicatorColor,
            strokeWidth = strokeWidth
        )


        Spacer(modifier = Modifier.height(24.dp))

        Button(
            onClick = {
                if (!isRunning) {
                    isRunning = true
                    coroutineScope.launch {
                        while (timeLeft > 0) {
                            delay(1000)
                            timeLeft--
                        }
                        isRunning = false
                    }
                }
            },
            enabled = !isRunning // Disable button while timer is running
        ) {
            Text(if (isRunning) "Идет..." else "Старт")
        }
    }
}

fun formatTime(seconds: Int): String {
    val minutes = seconds / 60
    val remainingSeconds = seconds % 60
    return String.format("%02d:%02d", minutes, remainingSeconds)
}

@Composable
fun CounterScreen() {
    var emailCount by remember { mutableStateOf(0) }
    var callCount by remember { mutableStateOf(0) }
    var showAllReadMessage by remember { mutableStateOf(false) }

    // Customize badge colors
    val emailBadgeBackgroundColor = Color.Red
    val emailBadgeTextColor = Color.White
    val callBadgeBackgroundColor = Color.Green
    val callBadgeTextColor = Color.Black

    Column(
        modifier = Modifier
            .fillMaxSize()
            .padding(16.dp),
        horizontalAlignment = Alignment.CenterHorizontally,
        verticalArrangement = Arrangement.spacedBy(16.dp)
    ) {
        Row(
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(8.dp)
        ) {
            IconWithBadge(
                icon = Icons.Filled.Email,
                count = emailCount,
                badgeBackgroundColor = emailBadgeBackgroundColor,
                badgeTextColor = emailBadgeTextColor,
                onIconClick = {
                    emailCount = 0
                    showAllReadMessage = true
                }
            )
            IconWithBadge(
                icon = Icons.Filled.Call,
                count = callCount,
                badgeBackgroundColor = callBadgeBackgroundColor,
                badgeTextColor = callBadgeTextColor,
                onIconClick = {} // Add functionality to call icon here
            )
        }

        if (showAllReadMessage) {
            Text("Все сообщения прочитаны", color = Color.Gray)
        }

        Button(onClick = {
            emailCount += Random.nextInt(1, 10) // Increase email count randomly
            callCount += Random.nextInt(1, 5) // Increase call count randomly
            showAllReadMessage = false
        }) {
            Text("Обновить")
        }
    }
}

@Composable
fun IconWithBadge(
    icon: ImageVector,
    count: Int,
    badgeBackgroundColor: Color,
    badgeTextColor: Color,
    onIconClick: () -> Unit // Callback for icon click
) {
    Box(contentAlignment = Alignment.TopEnd) {
        Icon(
            imageVector = icon,
            contentDescription = null,
            modifier = Modifier
                .size(48.dp)
                .clickable { onIconClick() }
        )

        if (count > 0) {
            Text(
                text = count.toString(),
                style = TextStyle(color = badgeTextColor, fontSize = 12.sp, textAlign = TextAlign.Center),
                modifier = Modifier
                    .size(24.dp)
                    .clip(CircleShape)
                    .background(badgeBackgroundColor)
            )
        }
    }
}

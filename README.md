# Snake-Source-Code 
Snake for linux make with phyton the polish version
To install it you need phyton type: 
sudo apt install python3-curses
after install paste this:
cat > snake.py <<'PY'
import curses
import random

def main(stdscr):
    curses.curs_set(0)
    stdscr.nodelay(True)
    stdscr.keypad(True)

    h, w = stdscr.getmaxyx()
    if h < 12 or w < 30:
        stdscr.nodelay(False)
        stdscr.addstr(0, 0, "Powieksz terminal i uruchom ponownie gre.")
        stdscr.getch()
        return

    snake = [(h // 2, w // 2), (h // 2, w // 2 - 1),
             (h // 2, w // 2 - 2)]
    direction = (0, 1)
    food = (h // 2, w // 2 + 5)
    score = 0
    delay = 110

    def new_food():
        while True:
            p = (random.randint(1, h - 2),
                 random.randint(1, w - 2))
            if p not in snake:
                return p

    while True:
        stdscr.erase()
        stdscr.border()
        stdscr.addstr(0, 2, f" SNAKE | Punkty: {score} | Q = wyjscie ")

        key = stdscr.getch()
        turns = {
            curses.KEY_UP: (-1, 0), ord('w'): (-1, 0),
            curses.KEY_DOWN: (1, 0), ord('s'): (1, 0),
            curses.KEY_LEFT: (0, -1), ord('a'): (0, -1),
            curses.KEY_RIGHT: (0, 1), ord('d'): (0, 1)
        }

        if key in (ord('q'), ord('Q')):
            break

        if key in turns:
            new_dir = turns[key]
            if new_dir != (-direction[0], -direction[1]):
                direction = new_dir

        head = (snake[0][0] + direction[0],
                snake[0][1] + direction[1])

        growing = head == food
        body = snake if growing else snake[:-1]

        if (head[0] <= 0 or head[0] >= h - 1 or
            head[1] <= 0 or head[1] >= w - 1 or head in body):
            stdscr.nodelay(False)
            msg = "KONIEC GRY! Nacisnij dowolny klawisz."
            stdscr.addstr(h // 2, max(1, (w - len(msg)) // 2), msg)
            stdscr.getch()
            break

        snake.insert(0, head)

        if growing:
            score += 1
            food = new_food()
            delay = max(45, 110 - score * 2)
        else:
            snake.pop()

        stdscr.addch(food[0], food[1], '*')
        for y, x in snake:
            stdscr.addch(y, x, '#' if (y, x) == snake[0] else 'o')

        stdscr.refresh()
        curses.napms(delay)

curses.wrapper(main)

main
PY


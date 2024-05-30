<html lang="en">
<head>
    <style>
        @keyframes strobe {
            0%, 100%{
                border-color: #FF0000;
            }
            16% {
                border-color: #FFFF00;
            }
            33% {
                border-color: #00FF00;
            }
            49% {
                border-color: #00FFFF
            }
            66% {
                border-color: #0000FF;
            }
            80% {
                border-color: #FF00FF;
            }
        }
        body {
            margin: 0;
            font-family: Arial, sans-serif;
            background-color: #171515;
            color: #39FF14;
            display: flex;
            flex-direction: column;
            min-height: 100vh;
        }
        .container {
            border-radius: 15px;
            padding: 20px;
            border: 5px solid transparent;
            background-clip: padding-box;
            background-color: #171515;
            color: #39FF14;
            animation: strobe 2s infinite; /* Apply strobe light effect to the border */
            max-width: 1000px;
            width: 100%; /* Adjusted width */
            margin: 20px auto; /* Center the container */
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
        }
        input[type="text"],
        textarea {
            width: calc(100% - 20px); /* Subtract padding and border from width */
            margin-bottom: 10px;
            padding: 10px;
            border: 1px solid #ccc;
            background-color: #fff;
            color: #000;
            border-radius: 5px;
            resize: vertical;
            box-sizing: border-box; /* Include padding and border in the width calculation */
        }
        button {
            width: calc(100% - 20px); /* Subtract padding and border from width */
            padding: 10px;
            background-color: #39dd14;
            color: #fff;
            border: none;
            cursor: pointer;
            border-radius: 5px;
            transition: background-color 0.3s ease;
            box-sizing: border-box; /* Include padding and border in the width calculation */
        }
        button:hover {
            background-color: #2d5e00;
        }
        .posts-container {
            max-width: 800px;
            width: 100%;
            padding: 20px;
            flex: 1; /* Take remaining space */
            overflow-y: auto; /* Add vertical scrollbar if needed */
            display: flex;
            flex-direction: column;
            align-items: center;
        }
        .post-container {
            width: 100%;
            max-width: 600px;
            border: 1px solid #ccc;
            margin-bottom: 10px;
            padding: 10px;
            background-color: #000;
            color: #fff;
            border-radius: 5px;
            position: relative;
            box-sizing: border-box; /* Include padding and border in the width calculation */
        }
        .post-actions {
            display: flex;
            justify-content: flex-end;
            margin-top: 10px;
        }
        .post-actions button {
            margin-left: 5px;
            cursor: pointer;
            background-color: transparent;
            border: none;
            color: #39FF14;
        }
        .post-content {
            margin: 0; /* Remove default margin for <p> */
        }
        .reply-form-container {
            margin-top: 10px;
            border: 1px solid #ccc;
            padding: 10px;
            background-color: #252525; /* Change the background color to a different color */
            border-radius: 5px;
            color: #fff; /* Text color */
            width: calc(100% - 40px); /* Adjust width to make it smaller */
            margin-left: auto; /* Indent to the right */
        }
        .reply-form-container h3 {
            margin-top: 0;
        }
        .dropdown-button {
            padding: 10px 20px;
            font-size: 16px;
            cursor: pointer;
        }
        .dropdown-menu {
            display: none;
            position: absolute;
            background-color: white;
            border: 1px solid #ccc;
            box-shadow: 0 2px 8px rgba(0, 0, 0, 0.15);
            z-index: 100;
        }
        .dropdown-menu a {
            display: block;
            padding: 10px 20px;
            text-decoration: none;
            color: black;
        }
        .dropdown-menu a:hover {
            background-color: #f0f0f0;
        }
    </style>
</head>
<body onload="fetchPosts();">
    <div class="container">
        <div class="input-container">
            <form action="javascript:createPost()" id="postButton">
                <h2>Post Your Message</h2>
                <textarea id="message" placeholder="Type your post..."></textarea>
                <button id="postButton">Post</button>
            </form>
            <button onclick="fetchMostActiveUserAndLikes()">Show Most Active User and Total Likes</button>
            <div id="mostActiveUser"></div>
            <div id="totalLikes"></div>
        </div>
        <div class="posts-container" id="postsWrapper">
            <h2>Posts</h2>
            <input type="text" id="searchInput" oninput="searchPosts()" placeholder="Search posts...">
            <div style="position: relative;">
                <button class="dropdown-button" onclick="toggleDropdown()">Sort By</button>
                <div class="dropdown-menu" id="dropdownMenu">
                    <a href="#" onclick="sort_by_likes()">Sort by Likes</a>
                    <a href="#" onclick="sort_by_date()">Sort by Date</a>
                </div>
            </div>
            <div id="posts"></div>
        </div>
    </div>
    <div id="latestPosts" class="latest-posts"></div>
    <script>
        let uri = "http://localhost:8086/";
        if (location.hostname === "127.0.0.1") {
            uri = "http://127.0.0.1:8086/";
        } else if (location.hostname === "0.0.0.0") {
            uri = "http://0.0.0.0:4100/";
        }
        function createPost() {
            var myHeaders = new Headers();
            myHeaders.append("Content-Type", "application/json");
            const message = document.getElementById("message").value;
            const body = {
                message: message,
                likes: 0
            };
            const authOptions = {
                method: 'POST',
                cache: 'no-cache',
                headers: myHeaders,
                body: JSON.stringify(body),
                credentials: 'include'
            };
            fetch(uri + '/api/messages/send', authOptions)
                .then(response => {
                    if (!response.ok) {
                        console.error('Failed to create post:', response.status);
                        return null;
                    }
                    const contentType = response.headers.get('Content-Type');
                    if (contentType && contentType.includes('application/json')) {
                        return response.json();
                    } else {
                        return response.text();
                    }
                })
                .then(data => {
                    if (data !== null) {
                        console.log('Response:', data);
                        updatePostsContainer(data['uid'], message, 0);
                    }
                })
                .catch(error => {
                    console.error('Error:', error);
                });
        }
        function fetchPosts() {
            var myHeaders = new Headers();
            myHeaders.append("Content-Type", "application/json");
            const authOptions = {
                method: 'GET',
                cache: 'no-cache',
                headers: myHeaders,
                credentials: 'include'
            };
            fetch(uri + '/api/messages/', authOptions)
                .then(response => {
                    if (!response.ok) {
                        console.error('Failed to fetch posts:', response.status);
                        return null;
                    }
                    return response.json();
                })
                .then(posts => {
                    if (posts === null || posts === undefined) {
                        console.warn('Received null or undefined posts.');
                        alert('Please Log in first!');
                        return;
                    }
                    console.log('Fetched Posts:', posts);
                    displayPosts(posts);
                })
                .catch(error => {
                    console.error('Error:', error);
                });
        }
        function displayPosts(posts) {
            const postsContainer = document.getElementById('posts');
            postsContainer.innerHTML = ''; // Clear existing posts
            posts.forEach(post => {
                updatePostsContainer(post.uid, post.message, post.likes);
            });
        }
function updatePostsContainer(uid, message, likes) {
    const postsContainer = document.getElementById('posts');
    const postDiv = document.createElement('div');
    postDiv.className = 'post-container';
    postDiv.dataset.uid = uid; // Assigning uid as a dataset attribute
    // Create a separate element to display likes
    const likesDiv = document.createElement('div');
    likesDiv.className = 'likes-container';
    likesDiv.textContent = `Likes: ${likes}`;
    const postContent = document.createElement('p');
    postContent.className = 'post-content';
    postContent.textContent = `${uid}: ${message}`;
    const editButton = document.createElement('button'); // Edit button
    editButton.textContent = 'Edit'; // Set text content to 'Edit'
    editButton.addEventListener('click', () => showEditForm(uid, message)); // Call showEditForm function
    const likeButton = document.createElement('button');
    likeButton.textContent = `Like`;
    likeButton.addEventListener('click', () => updateLikeCount(uid, message));
    const deleteButton = document.createElement('button');
    deleteButton.textContent = 'Delete';
    deleteButton.addEventListener('click', () => deletePost(uid, message));
    const replyButton = document.createElement('button');
    replyButton.textContent = 'Reply';
    replyButton.addEventListener('click', () => toggleReplyForm(postDiv));
    const actionsDiv = document.createElement('div');
    actionsDiv.className = 'post-actions';
    actionsDiv.appendChild(editButton);
    actionsDiv.appendChild(likeButton);
    actionsDiv.appendChild(deleteButton);
    actionsDiv.appendChild(replyButton);
    postDiv.appendChild(postContent);
    postDiv.appendChild(actionsDiv);
    postDiv.appendChild(likesDiv); // Append the likes container
    postsContainer.appendChild(postDiv);
}
        function toggleReplyForm(postDiv) {
            const existingReplyForm = postDiv.querySelector('.reply-form-container');
            if (existingReplyForm) {
                existingReplyForm.remove();
                return;
            }
            const replyFormContainer = document.createElement('div');
            replyFormContainer.className = 'reply-form-container';
            const replyForm = document.createElement('form');
            replyForm.action = 'javascript:void(0);'; // Prevent form submission
            const replyTextarea = document.createElement('textarea');
            replyTextarea.placeholder = 'Type your reply...';
            const replyButton = document.createElement('button');
            replyButton.textContent = 'Reply';
            replyButton.addEventListener('click', () => sendReply(postDiv.dataset.uid, replyTextarea.value));
            replyForm.appendChild(replyTextarea);
            replyForm.appendChild(replyButton);
            replyFormContainer.appendChild(replyForm);
            postDiv.appendChild(replyFormContainer);
        }
        function sendReply(uid, replyMessage) {
            if (!replyMessage.trim()) {
                return;
            }
            const myHeaders = new Headers();
            myHeaders.append("Content-Type", "application/json");
            const body = {
                message: replyMessage,
                likes: 0,
                parentUid: uid
            };
            const authOptions = {
                method: 'POST',
                cache: 'no-cache',
                headers: myHeaders,
                body: JSON.stringify(body),
                credentials: 'include'
            };
            fetch(uri + '/api/messages/send', authOptions)
                .then(response => {
                    if (!response.ok) {
                        console.error('Failed to send reply:', response.status);
                        return null;
                    }
                    return response.json();
                })
                .then(data => {
                    if (data !== null) {
                        console.log('Reply sent:', data);
                        // Update the post with the new reply
                        const postDiv = document.querySelector(`[data-uid="${uid}"]`);
                        if (postDiv) {
                            const replyContainer = postDiv.querySelector('.reply-form-container');
                            if (replyContainer) {
                                replyContainer.remove(); // Remove reply form after sending reply
                            }
                            // Create new reply element
                            const replyDiv = document.createElement('div');
                            replyDiv.className = 'post-container';
                            const replyContent = document.createElement('p');
                            replyContent.className = 'post-content';
                            replyContent.textContent = `${data.uid}: ${replyMessage}`;
                            replyDiv.appendChild(replyContent);
                            postDiv.appendChild(replyDiv); // Add reply to the post
                        }
                    }
                })
                .catch(error => {
                    console.error('Error:', error);
                });
        }
        function updateLikeCount(uid, message) {
    const myHeaders = new Headers();
    myHeaders.append("Content-Type", "application/json");
    const body = {
        message: message
    };
    const authOptions = {
        method: 'PUT',
        cache: 'no-cache',
        headers: myHeaders,
        body: JSON.stringify(body),
        credentials: 'include'
    };
    fetch(uri + '/api/messages/like', authOptions)
        .then(response => {
            if (!response.ok) {
                console.error('Failed to like post:', response.status);
                return null;
            }
            return response.json();
        })
        .then(data => {
            if (data !== null) {
                console.log('Like updated:', data);
                const postDiv = document.querySelector(`[data-uid="${uid}"]`);
                const likeButton = postDiv.querySelector('button:nth-child(2)');
                const likesContainer = postDiv.querySelector('.likes-container');
                likeButton.textContent = `Like (${data})`;
                likesContainer.textContent = `Likes: ${data}`; // Update the like counter displayed on the post
                likeButton.disabled = true; // Disable the like button
                likeButton.style.display = 'none'; // Hide the like button
            }
        })
        .catch(error => {
            console.error('Error:', error);
        });
}
        function deletePost(uid, message) {
            console.log(message)
            const myHeaders = new Headers();
            myHeaders.append("Content-Type", "application/json");
            body = {message: message}
            const authOptions = {
                method: 'DELETE',
                cache: 'no-cache',
                headers: myHeaders,
                credentials: 'include',
                body: JSON.stringify(body)
            };
            fetch(uri + `/api/messages/delete`, authOptions)
                .then(response => {
                    if (!response.ok) {
                        alert('Users can only delete their own message!');
                        console.error('Failed to delete post:', response.status);
                        return null;
                    }
                    return response.json();
                })
                .then(data => {
                    if (data !== null) {
                        console.log('Post deleted:', data);
                        const postDiv = document.querySelector(`[data-uid="${uid}"]`);
                        postDiv.remove();
                    }
                })
                .catch(error => {
                    console.error('Error:', error);
                });
        }
        function showEditForm(uid, currentMessage) {
            const postDiv = document.querySelector(`[data-uid="${uid}"]`);
            const existingEditForm = postDiv.querySelector('.edit-form-container');
            if (existingEditForm) {
                existingEditForm.remove();
                return;
            }
            const editFormContainer = document.createElement('div');
            editFormContainer.className = 'edit-form-container';
            const editForm = document.createElement('form');
            editForm.action = 'javascript:void(0);'; // Prevent form submission
            const editTextarea = document.createElement('textarea');
            editTextarea.value = currentMessage;
            const editButton = document.createElement('button');
            editButton.textContent = 'Save';
            editButton.addEventListener('click', () => saveEdit(uid, editTextarea.value, currentMessage));
            editForm.appendChild(editTextarea);
            editForm.appendChild(editButton);
            editFormContainer.appendChild(editForm);
            postDiv.appendChild(editFormContainer);
        }
        function saveEdit(uid, newMessage, oldMessage) {
            if (!newMessage.trim()) {
                return;
            }
            const myHeaders = new Headers();
            myHeaders.append("Content-Type", "application/json");
            const body = {
                uid: uid,
                old_message: oldMessage,
                new_message: newMessage
            };
            const authOptions = {
                method: 'PUT',
                cache: 'no-cache',
                headers: myHeaders,
                body: JSON.stringify(body),
                credentials: 'include'
            };
            fetch(uri + `/api/messages/`, authOptions)
                .then(response => {
                    if (!response.ok) {
                        console.error('Failed to update post:', response.status);
                        alert('You are not the owner of this message!');
                    }
                    return response.json();
                })
                .then(data => {
                    if (data !== null) {
                        console.log('Post updated:', data);
                        fetchPosts();
                    }
                })
                .catch(error => {
                    console.error('Error:', error);
                });
        }
        function toggleDropdown() {
            const dropdownMenu = document.getElementById('dropdownMenu');
            dropdownMenu.style.display = dropdownMenu.style.display === 'block' ? 'none' : 'block';
        }
        function searchPosts() {
            const searchInput = document.getElementById('searchInput').value.toLowerCase();
            const posts = document.querySelectorAll('.post-container');
            posts.forEach(post => {
                const message = post.querySelector('.post-content').textContent.toLowerCase();
                if (message.includes(searchInput)) {
                    post.style.display = '';
                } else {
                    post.style.display = 'none';
                }
            });
        }
        function sort_by_date() {
            const myHeaders = new Headers();
            myHeaders.append("Content-Type", "application/json");
            const authOptions = {
                method: 'PUT',
                cache: 'no-cache',
                headers: myHeaders,
                credentials: 'include'
            };
            fetch(uri + '/api/messages/sort', authOptions)
                .then(response => {
                    if (!response.ok) {
                        console.error('Failed to fetch posts:', response.status);
                        return null;
                    }
                    return response.json();
                })
                .then(posts => {
                    if (posts === null || posts === undefined) {
                        console.warn('Received null or undefined posts.');
                        return;
                    }
                    console.log('Fetched Posts:', posts);
                    displayPosts(posts);
                })
                .catch(error => {
                    console.error('Error:', error);
                });
        }
        function sort_by_likes() {
            const myHeaders = new Headers();
            myHeaders.append("Content-Type", "application/json");
            const authOptions = {
                method: 'POST',
                cache: 'no-cache',
                headers: myHeaders,
                credentials: 'include'
            };
            fetch(uri + '/api/messages/sort', authOptions)
                .then(response => {
                    if (!response.ok) {
                        console.error('Failed to fetch posts:', response.status);
                        return null;
                    }
                    return response.json();
                })
                .then(posts => {
                    if (posts === null || posts === undefined) {
                        console.warn('Received null or undefined posts.');
                        return;
                    }
                    console.log('Fetched Posts:', posts);
                    displayPosts(posts);
                })
                .catch(error => {
                    console.error('Error:', error);
                });
        }
        function fetchMostActiveUserAndLikes() {
            fetch(uri + '/api/messages/like', {
                method: 'POST',
                cache: 'no-cache',
                credentials: 'include'
            })
            .then(response => response.json())
            .then(data => {
                if (data) {
                    document.getElementById('mostActiveUser').innerText = `Most Active User: ${data[1]}`;
                    document.getElementById('totalLikes').innerText = `Total Likes: ${data[0]}`;
                }
            })
            .catch(error => console.error('Error fetching stats:', error));
        }
    </script>
</body>
</html>